In this example, I will show how we can model the data of a complete B2C application and how to write complex queries on this data model. I am going to use the data model used by the [TPC-H benchmark](http://www.tpc.org/tpch) which is shown below.


![image](https://s3.amazonaws.com/b2cbucket/tpch_schema.png)



The idea behind this example is to show how to use Cassandra to design a real B2C application data model and how to write advance SQL queries with joins, grouping and sorting using Cassandra query language (CQL). I have chosen three queries from the TPCH benchmark that I think will cover most of the common query capabilities of SQL. The chosen queries are Q1, Q3 and Q4 as will be explained in the following sections.


The data used as input for this example is a small set from the data generated using TPCH DBGEN tool. The data is stored in CSV files that correspond to each object in the TPCH benchmark schema shown above (customer, supplier, part, lineitem, order , etc ...). For more details about how to generate the data, please have a look at [this](http://kejser.org/tpc-h-data-and-query-generation/) blog post. 

The complete code of this example can be found in Github along with the steps on how to run it. You can also change the used input data by changing the content of the [input files](https://github.com/alronz/B2C-Database-Selection-Implementations/tree/master/Cassandra/CassandraTPCHQueries/src/main/java/org/cassandra/tpcH/data) stored in the "data" folder.


##### Pricing Summary Report Query (Q1)

This query is used to report the amount of billed, shipped and returned items. The SQL query is shown below:

````
select   l_returnflag,   l_linestatus,   sum(l_quantity) as sum_qty,   sum(l_extendedprice) as sum_base_price,   sum(l_extendedprice*(1-l_discount)) as sum_disc_price,   sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge,   avg(l_quantity) as avg_qty,   avg(l_extendedprice) as avg_price,   avg(l_discount) as avg_disc,   count(*) as count_orderfrom   lineitemwhere   l_shipdate <= date '1998-12-01' - interval '[DELTA]' day (3)group by   l_returnflag,   l_linestatusorder by   l_returnflag,   l_linestatus;
````

As explained previously in the [data layout section](../Data Modeling/data_layout.md), writes are cheap in Cassandra and it is absolutely ok to duplicate your data. We also model our data around the query patterns that we are planning to have in our application and not around the entities or relationships of the data model. This means that if we are going to frequently run the above query in our application, then it make sense to create a separate table for it to get better performance and to allow the data to scale efficiently. 

Ofcourse first we will create the Keyspace that will hold the tables as seen below:

````
CREATE KEYSPACE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE WITH replication = {'class':'SimpleStrategy', 'replication_factor':1};
````

Then we will create a Cassandra table that will be used to serve the above query using as shown below:

````
CREATE TABLE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 
(
orderkey text,
linestatus text,
returnflag text,
quantity double,
extendedprice double,
discount double,
tax double,
shipdate timestamp,
PRIMARY KEY ((returnflag,linestatus),shipdate,orderkey,linenumber)
);
````

The most important thing that you need to consider very carefully before creating CQL tables is the primary key. The primary key can be a single key, a composite key or a compound key and based on the primary key the data will be written to the partitions. If you use a primary key that is not unique, the data will be overwritten. This means you should always ensure that the primary key is unique to avoid overwriting your data. The primary key contains also the partition key which could be a single key or a composite key. The partition key defines how the data will be distributed across the different nodes, which means we should choose partition keys that allows for equal data distribution across the nodes and not creating what is called a hot spot in one of the partitions. A hot spot is created when the data is distributed unequally among the partitions where one of the partitions contains most of the data and hence receives most of the read and write requests. Additionally, Cassandra support only two billion columns per partition and after this limit it will reject the write requests. If you also choose a partition key that is too high (too unique),  then each partition will have very little data which will negate the benefits of the wide row data model and you will have too little data in each partition that worth ordering. For all these reasons, it is quite important that we choose a unique primary key to prevent data from overwriting and a good partition key that is not too high or too low so that it will allow for a good data distribution across the cluster. 

From the above SQL query (TPCH Q1), we have to perform aggregate functions such as sum, avg and count on the data inside the lineitem table grouped by the returnflag, and the linestatus. In Cassandra aggregates are always performed in the partition level, this means that the data will always be grouped by the partition key. Hence we will have to make sure that the linestatus and returnflag are part of the composite key that will make the partition key. However if we use only returnflag and linestatus as the primary key, then the data will be overwritten in each partition since they are not unique. For example, we could have multiple rows in the lineitem table having the same linestatus and returnflag. So we add the orderkey and the linenumber as clustering columns to the primary key to form a compound key which will make the primary key unique and will prevent overwriting the data. So the primary key is ((returnflag,linestatus),orderkey,linenumber). And since we want to filter results using the shipdate, then it would be a good idea to include the shipdate column in the compound key as clustering column. So now our compound primary key is  ((returnflag,linestatus),shipdate,orderkey,linenumber). The shipdate should be the first clustering column since we want to run range queries against it. If you put the shipdate as the second clustering column, then if you want to run range queries on it you would need first to specify the value of the first clustering column using either '=' or 'IN'.  The partition key (returnflag,linestatus) is good for data distribution since each partition will contain the data of a specific returnflag and linestatus.


Now after creating the table we need to load it with data. This is done using the below function:


````
private void loadTpchQ1() {

        File file = new File("src/main/java/org/cassandra/tpcH/data/lineitem.txt");

        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line;

            while ((line = br.readLine()) != null) {
                String[] lineFields = line.split(Pattern.quote("|"));

                LOGGER.info(line.toString());

                String insertStatement = "INSERT INTO CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 (orderkey,linenumber,linestatus,returnflag,quantity," +
                        "extendedprice,discount,tax,shipdate) VALUES" +
                        " ('" + lineFields[0] +
                        "','" + lineFields[3] +
                        "','" + lineFields[9] +
                        "','" + lineFields[8] +
                        "'," + Double.valueOf(lineFields[4]) +
                        "," + Double.valueOf(lineFields[5]) +
                        "," + Double.valueOf(lineFields[6]) +
                        "," + Double.valueOf(lineFields[7]) +
                        ",'" + lineFields[10] + "') IF NOT EXISTS;";

                LOGGER.info(insertStatement);

                session.execute(insertStatement);
            }


        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
}
````

After loading the data, now if we run a "select * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1;" statement we will see the below:


![image](https://s3.amazonaws.com/b2cbucket/q1screenshot1.png)



As mentioned in a [previous sections](../Search Data/queryOptions.md), Cassandra supports some built in aggregate functions such as sum, avg, min, max and count. However if you want to perform some mathematical operations within the SELECT statement, you will have to create a separate user defined function for that. As seen in the SQL query, we need to perform the below mathematical operations in the SELECT statement:

````
sum(l_extendedprice*(1-l_discount)) as sum_disc_price
// And
sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge
````

For that we need to create two user defined functions as shown below:

````
CREATE OR REPLACE FUNCTION  CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice (l_extendedprice double,l_discount double) CALLED ON NULL INPUT RETURNS double LANGUAGE java AS 'return (Double.valueOf( l_extendedprice.doubleValue() *  (1.0 - l_discount.doubleValue() ) ));
````

````
CREATE OR REPLACE FUNCTION CASSANDRA_EXAMPLE_KEYSPACE.fSumChargePrice (l_extendedprice double,l_discount double,l_tax double) CALLED ON NULL INPUT RETURNS double LANGUAGE java AS 'return (Double.valueOf( l_extendedprice.doubleValue() *  (1.0 - l_discount.doubleValue() ) * (1.0 + l_tax.doubleValue()) ));';
````


After creating the Keyspace, the CQL table, loaded the table with data ,  and created the two user defined functions fSumDiscPrice and fSumChargePrice, we can run a CQL SELECT query as shown below:


````
SELECT 
 returnflag,
 linestatus, 
 sum(quantity) as sum_qty,
 sum(extendedprice) as sum_base_price,
 sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(extendedprice,discount)) as sum_disc_price,
 sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumChargePrice(extendedprice,discount,tax)) as sum_charge, 
 avg(quantity) as avg_qty, avg(extendedprice) as avg_price, 
 avg(discount) as avg_disc, 
 count(*) as count_order 
FROM 
 CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 
WHERE 
 shipdate < '2000-01-01 22:00:00-0700'  
 and returnflag='N' 
 and linestatus = 'O' ;
````

Notice that we have specified the partition key values (returnflag and linestatus) which not what we want on the original query. We had to specify the partition key values since if you don't, Cassandra will aggregate the results across all the partitions in the cluster and gives wrong results beside it will be very slow. If you don't specify the partition key values, you will get a warning from Cassandra as shown below:


![image](https://s3.amazonaws.com/b2cbucket/q1screenshot3.png)


But this is not useful since we want to aggregate across the whole data in all the partition to get some kind of overall analytic report and not for each partition separately. To overcome this challenge, you can get the partition key values across all partitions using the below query :

````
select distinct  returnflag, linestatus from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 ;
````

For our example data, you will get only one row as shown below, but for different data you might get more than one row:

![image](https://s3.amazonaws.com/b2cbucket/q1screenshot4.png)

Then you can iterate through the results and run the CQL aggregate query shown above for each partition keys as shown below in the java code sinppest:

````

String getAllPartitionKeyValues = "select distinct  returnflag, linestatus from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 ;";

ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);

// get aggregate function per partition
for (Row row : rsAllPartitionKey.all()) {

String q1Statement = "SELECT returnflag, linestatus, sum(quantity) as sum_qty,sum(extendedprice) as sum_base_price, sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(extendedprice,discount)) as sum_disc_price\n" +                       ", sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumChargePrice(extendedprice,discount,tax)) as sum_charge, avg(quantity) as avg_qty,\n" + 
"avg(extendedprice) as avg_price, avg(discount) as avg_disc, count(*) as count_order \n" +
"FROM CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 WHERE shipdate < '2000-01-01 22:00:00-0700'  and returnflag = '" + row.getString("returnflag") + "' and linestatus = '" + row.getString("linestatus") + "' ALLOW FILTERING ; ";
                
ResultSet rsQ1 = this.model.executeStatement(q1Statement);

for (Row innerRow : rsQ1.all())
   rowsResult.add(innerRow);
}

````

You might be wondering about the "order by" clause in the SQL query. Unfortunately, you can't group and order by the same columns in Cassandra. Grouping in Cassandra is done using the partition key while ordering happens inside each partition using what is called the clustering columns. To put it simple, you can't group by a column (use it as a partition key) and then order by the same column using the ORDER BY clause since you can order by only a clustering column. Although there is a way to order cluster by the partition keys using the Byte Ordered Partitioner (BOP), however it is discouraged because it can lead to hot spots and load balancing problems.


The complete java code for the api that will return the results of TPCH Q1 is shown below:

````
@GET
    @Path("/q1")
    @Timed
    @ApiOperation(value = "get result of TPCH Q1 using this model", notes = "Returns String", response = String.class)
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public String getQ1Results() {


        try {

            this.model.connect();

            ArrayList<Row> rowsResult = new ArrayList<Row>();

            String createIndexOnCommitDate = "CREATE INDEX IF NOT EXISTS shipdate_index ON CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 (shipdate)";

            this.model.executeStatement(createIndexOnCommitDate);


            String getAllPartitionKeyValues = "select distinct  returnflag, linestatus from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);

            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {

                String q1Statement = "SELECT returnflag, linestatus, sum(quantity) as sum_qty,sum(extendedprice) as sum_base_price, sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(extendedprice,discount)) as sum_disc_price\n" +
                        ", sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumChargePrice(extendedprice,discount,tax)) as sum_charge, avg(quantity) as avg_qty,\n" +
                        "avg(extendedprice) as avg_price, avg(discount) as avg_disc, count(*) as count_order \n" +
                        "FROM CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q1 WHERE shipdate < '2000-01-01 22:00:00-0700'  and returnflag = '" + row.getString("returnflag") + "' and linestatus = '" + row.getString("linestatus") + "' ALLOW FILTERING ; ";
                ResultSet rsQ1 = this.model.executeStatement(q1Statement);

                for (Row innerRow : rsQ1.all())
                    rowsResult.add(innerRow);
            }


            this.model.close();

            return rowsResult.toString();
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }


````



##### Shipping Priority Query (Q3)

This query is used to get the 10 unshipped orders with the highest value. In order to do that, the query joins three tables (customer, order and lineitem) as shown below:


````
select l_orderkey, sum(l_extendedprice*(1-l_discount)) as revenue, o_orderdate, o_shippriorityfrom customer, orders, lineitemwhere c_mktsegment = '[SEGMENT]' and c_custkey = o_custkey and l_orderkey = o_orderkey and o_orderdate < date '[DATE]' and l_shipdate > date '[DATE]'group by l_orderkey, o_orderdate, o_shippriorityorder by revenue desc, o_orderdate;
````

Since joins aren't supported in Cassandra, we create a CQL table that has all the needed values from the different tables since duplicating your data is ok in Cassandra as mentioned in the [data layout section](../Data Modeling/data_layout.md). We create the table as shown below:

````
 CREATE TABLE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3
(
orderkey text,
linenumber text,
o_orderdate timestamp,
o_shippriority text,
c_mktsegment text,
l_extendedprice double,
l_discount double,
l_shipdate timestamp,
PRIMARY KEY ((orderkey,o_orderdate,o_shippriority),c_mktsegment,l_shipdate,linenumber)
);
````


Similar to what we have done in Q1, we have chosen the partition key to be the columns that we are going to group with as shown in the SQL query (l_orderkey,o_orderdate,o_shippriority). This will let us run aggregate functions on them since the aggregate functions are run in partition level in Cassandra. Then we have added the linenumber to the compound primary key so that the primary key is unique and to avoid overwriting. I have added the c_mktsegment and the l_shipdate as clustering columns to the compound primary key since we need to query against them. If you don't want to use it as a clustering column, you can also create a separate secondary indexes for the columns you want to filter with. 

Now we need to load the table with data as shown below:

````

private void loadTpchQ3() {

        File file = new File("src/main/java/org/cassandra/tpcH/data/order.txt");


        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line;

            while ((line = br.readLine()) != null) {
                String[] lineFields = line.split(Pattern.quote("|"));

                // get c_mktsegment based on the custkey
                String c_mktsegment = null;

                File customerFile = new File("src/main/java/org/cassandra/tpcH/data/customer.txt");
                String customerLine;
                BufferedReader customerBr = new BufferedReader(new FileReader(customerFile));

                while ((customerLine = customerBr.readLine()) != null) {
                    String[] customerLineFields = customerLine.split(Pattern.quote("|"));

                    if (lineFields[1].equals(customerLineFields[0])) {
                        c_mktsegment = customerLineFields[6];
                    }
                }


                File lineItems = new File("src/main/java/org/cassandra/tpcH/data/lineitem.txt");
                String lineItemLine;
                BufferedReader lineItemBr = new BufferedReader(new FileReader(lineItems));

                while ((lineItemLine = lineItemBr.readLine()) != null) {
                    String[] lineItemLineFields = lineItemLine.split(Pattern.quote("|"));

                    if (lineItemLineFields[0].equals(lineFields[0])) {

                        String insertStatement = "INSERT INTO CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3 (orderkey,linenumber,o_orderdate,o_shippriority," +
                                "c_mktsegment,l_extendedprice,l_discount,l_shipdate) VALUES" +
                                " ('" + lineFields[0] +
                                "','" + lineItemLineFields[3] +
                                "','" + lineFields[4] +
                                "','" + lineFields[5] +
                                "','" + c_mktsegment +
                                "'," + Double.valueOf(lineItemLineFields[5]) +
                                "," + Double.valueOf(lineItemLineFields[6]) +
                                ",'" + lineItemLineFields[10] + "') IF NOT EXISTS";

                        ResultSet rs = session.execute(insertStatement);
                        LOGGER.info(rs.toString());
                    }
                }
            }


        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }


````


After the data is loaded in the table. If you run a "select * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3;", you will get the below:


![image](https://s3.amazonaws.com/b2cbucket/q3screenshot1.png)


Now after we have created the table and loaded it with date, we can run aggregate query like the SQL version as shown below:

````
SELECT orderkey,sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(l_extendedprice,l_discount)) as revenue, o_orderdate,l_shipdate, o_shippriority,linenumber from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3
 where orderkey= 'somekey'  and o_orderdate = 'somedate' and o_shippriority='somepriority'  and c_mktsegment='Segement' and l_shipdate > '1990-01-01';
````

As you can see, we have specified the partition key values since it is necessary to get correct aggregated results. However we can run it across all partitions by retrieving all the partition values and then iterate through them to run the above query in each partition as shown below:


````

String getAllPartitionKeyValues = "select distinct  orderkey, o_orderdate,o_shippriority from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);


            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {
                String q3TempStatement = "SELECT orderkey,sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(l_extendedprice,l_discount)) as revenue, o_orderdate,l_shipdate, o_shippriority,linenumber from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3\n" +
                        " where orderkey= '" + row.getString("orderkey") + "'  and o_orderdate = '" + formatter.format(row.getTimestamp("o_orderdate")) + "' and o_shippriority='" + row.getString("o_shippriority") + "'  and c_mktsegment='AUTOMOBILE' and l_shipdate > '1990-01-01' ;";


                ResultSet rsQ3Temp = this.model.executeStatement(q3TempStatement);


                if (rsQ3Temp != null) {

                    for (Row innerRow : rsQ3Temp.all()) {

                        if (innerRow != null && !innerRow.isNull("orderkey")) {
                        
                        for (Row innerRow : rsQ1.all())
                              rowsResult.add(innerRow);

                        }
                    }
                }
            }

````


As you can see from the SQL query, the results are sorted by the revenue which is a field that was created using an aggregate function during the SELECT statement. Unfortunately this is not easily done in Cassandra. The sorting columns (clustering columns) are defined during the table creation and not during the SELECT statement. One idea to solve this, is to create a Materialised View that will be created using a SELECT statement and then use the newly created column (revenue) to be the clustering column of the created Materialised View. However unfortunately Cassandra doesn't support user defined functions on the Materialised View that would be needed to create the revenue field ( sum(l_extendedprice*(1-l_discount)) as revenue). Another idea I used to overcome this challenge is by creating a temporary table to store the intermediate aggregate results, then query this table and finally delete it once finished. The temporary table has the below structure:

````
CREATE TABLE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP
orderkey text,
revenue double,
o_orderdate timestamp,
o_shippriority text,
PRIMARY KEY (orderkey,revenue,o_orderdate) 
)WITH CLUSTERING ORDER BY (revenue DESC, o_orderdate ASC);
````

As you can see, it has a simple partition key (orderkey) as it would contain only the results of the aggregate query. The revenue and o_orderdate are the clustering columns used for sorting. I have also specified the sorting order using the WITH CLUSTERING ORDER BY clause. 

Now you can load it with results of the aggregate query of each partition as show below:


````

String getAllPartitionKeyValues = "select distinct  orderkey, o_orderdate,o_shippriority from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);


            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {
                String q3TempStatement = "SELECT orderkey,sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(l_extendedprice,l_discount)) as revenue, o_orderdate,l_shipdate, o_shippriority,linenumber from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3\n" +
                        " where orderkey= '" + row.getString("orderkey") + "'  and o_orderdate = '" + formatter.format(row.getTimestamp("o_orderdate")) + "' and o_shippriority='" + row.getString("o_shippriority") + "'  and c_mktsegment='AUTOMOBILE' and l_shipdate > '1990-01-01' ;";


                ResultSet rsQ3Temp = this.model.executeStatement(q3TempStatement);


                if (rsQ3Temp != null) {

                    for (Row innerRow : rsQ3Temp.all()) {

                        if (innerRow != null && !innerRow.isNull("orderkey")) {

                            String insertStatement = "INSERT INTO CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP (orderkey,revenue,o_orderdate," +
                                    "o_shippriority) VALUES" +
                                    " ('" + innerRow.getString("orderkey") +
                                    "'," + Double.valueOf(innerRow.getDouble("revenue")) +
                                    ",'" + formatter.format(innerRow.getTimestamp("o_orderdate")) +
                                    "','" + innerRow.getString("o_shippriority") + "') IF NOT EXISTS;";

                            this.model.executeStatement(insertStatement);

                            LOGGER.info("insert executed");
                        }
                    }
                }
            }

````



Then you can run queries against this temporary table as below:

````
SELECT * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP ;
````

Notice that we don't use any ORDER BY clause since it will be sorted by the order specified when we created the table.  We get the results as shown below:

![image](https://s3.amazonaws.com/b2cbucket/q3screenshot2.png)


The complete api function used to retrieve Q3 is shown below:


````
 @GET
    @Path("/q3")
    @Timed
    @ApiOperation(value = "get result of TPCH Q3 using this model", notes = "Returns String", response = String.class)
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public String getQ2Results() {


        try {

            String tpchQ3TempTable = " CREATE TABLE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP\n" +
                    " (\n" +
                    " orderkey text,\n" +
                    " revenue double,\n" +
                    " o_orderdate timestamp,\n" +
                    " o_shippriority text,\n" +
                    " PRIMARY KEY (orderkey,revenue,o_orderdate) \n" +
                    " )WITH CLUSTERING ORDER BY (revenue DESC, o_orderdate ASC);";


            this.model.connect();
            this.model.executeStatement(tpchQ3TempTable);

            String getAllPartitionKeyValues = "select distinct  orderkey, o_orderdate,o_shippriority from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);


            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {
                String q3TempStatement = "SELECT orderkey,sum(CASSANDRA_EXAMPLE_KEYSPACE.fSumDiscPrice(l_extendedprice,l_discount)) as revenue, o_orderdate,l_shipdate, o_shippriority,linenumber from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3\n" +
                        " where orderkey= '" + row.getString("orderkey") + "'  and o_orderdate = '" + formatter.format(row.getTimestamp("o_orderdate")) + "' and o_shippriority='" + row.getString("o_shippriority") + "'  and c_mktsegment='AUTOMOBILE' and l_shipdate > '1990-01-01' ;";


                ResultSet rsQ3Temp = this.model.executeStatement(q3TempStatement);


                if (rsQ3Temp != null) {

                    for (Row innerRow : rsQ3Temp.all()) {

                        if (innerRow != null && !innerRow.isNull("orderkey")) {

                            String insertStatement = "INSERT INTO CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP (orderkey,revenue,o_orderdate," +
                                    "o_shippriority) VALUES" +
                                    " ('" + innerRow.getString("orderkey") +
                                    "'," + Double.valueOf(innerRow.getDouble("revenue")) +
                                    ",'" + formatter.format(innerRow.getTimestamp("o_orderdate")) +
                                    "','" + innerRow.getString("o_shippriority") + "') IF NOT EXISTS;";

                            this.model.executeStatement(insertStatement);

                            LOGGER.info("insert executed");
                        }
                    }
                }
            }


            String q3FinalStatement = "SELECT * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP ; ";


            ResultSet rsQ3 = this.model.executeStatement(q3FinalStatement);

            String dropQ3TempTable = "DROP TABLE CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3_TEMP;";

            ResultSet rsQ3DropMV = this.model.executeStatement(dropQ3TempTable);
            LOGGER.info(rsQ3DropMV.toString());

            this.model.close();

            return rsQ3.all().toString();
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }

````




##### Order Priority Checking Query (Q4)

This query is used to see how well the order priority system is working and gives indication of the customer satisfaction. The SQL query is shown below:

````
select o_orderpriority, count(*) as order_countfrom
 orderswhere o_orderdate >= date '[DATE]' and o_orderdate < date '[DATE]' + interval '3' month and exists (select *from lineitemwhere l_orderkey = o_orderkey and l_commitdate < l_receiptdate)group by o_orderpriorityorder by o_orderpriority;
````

In a similar way like we have done in Q1 and Q3 , we create a new CQL table that will be used to serve the above query. The table is shown below:

````
CREATE TABLE CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4
(
orderkey text,
linenumber text,
o_orderpriority text,
o_orderdate timestamp,
l_receiptdate timestamp,
l_commitdate timestamp,
PRIMARY KEY (o_orderpriority,o_orderdate,orderkey,linenumber)
)
````


We have used the primary key to be a combination of o_orderpriority, o_orderdate, orderkey, and linenumber to ensure the uniqueness of the inserted data in each partition. The o_orderpriority is the partition key since it is the group by column in the original SQL query. And as explained before, aggregation in Cassandra is done in the partition level so we have to use the o_orderpriority as the partition key. Note that we have used o_orderdate as the first clustering column so that we can run range queries on it. However we will have to create a secondary index for the l_commitdate since we can't have two clustering columns and still be able to run range queries on both of them. Hence I have created a secondary index on the l_commitdate as shown below:


````
CREATE INDEX IF NOT EXISTS l_commitdate_index_on_TPCH_Q4 ON CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 (l_commitdate);
````

Then we load the table with data using the below method:

````
private void loadTpchQ4() {

        File file = new File("src/main/java/org/cassandra/tpcH/data/order.txt");


        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line;

            while ((line = br.readLine()) != null) {
                String[] lineFields = line.split(Pattern.quote("|"));

                File lineItems = new File("src/main/java/org/cassandra/tpcH/data/lineitem.txt");
                String lineItemLine;
                BufferedReader lineItemBr = new BufferedReader(new FileReader(lineItems));

                while ((lineItemLine = lineItemBr.readLine()) != null) {
                    String[] lineItemLineFields = lineItemLine.split(Pattern.quote("|"));

                    if (lineItemLineFields[0].equals(lineFields[0])) {

                        String insertStatement = "INSERT INTO CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 (linenumber,orderkey,o_orderpriority,o_orderdate,l_receiptdate," +
                                "l_commitdate) VALUES" +
                                " ('" + lineItemLineFields[3] +
                                "','" + lineFields[0] +
                                "','" + lineFields[5] +
                                "','" + lineFields[4] +
                                "','" + lineItemLineFields[12] +
                                "','" + lineItemLineFields[11] + "') IF NOT EXISTS";

                        ResultSet rs = session.execute(insertStatement);
                        LOGGER.info(rs.toString());
                    }
                }
            }


        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }


````



After loading the data, if you run a "SELECT * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4;" you will get the below results:

![image](https://s3.amazonaws.com/b2cbucket/q4screenshot1.png)
 

Now we can run an aggregate query against our table but first we need to get all the partition keys. Then we can run the query against each partition as we have done in Q1 and Q3 as shown below:


````

String getAllPartitionKeyValues = "select distinct  o_orderpriority from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);

            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {

                String q4Statement = "SELECT o_orderpriority, count(*) as order_count from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 \n" +
                        "where  o_orderpriority ='" + row.getString("o_orderpriority") + "' and o_orderdate >=  '1990-01-01' and o_orderdate < '2000-01-01' and l_commitdate < '2000-01-01' ALLOW FILTERING ";


                ResultSet rsQ4 = this.model.executeStatement(q4Statement);

                for (Row innerRow : rsQ4.all())
                    rowsResult.add(innerRow);
            }

````


You can notice that I couldn't run the SQL condition ( and l_commitdate < l_receiptdate) in Cassandra since it isn't supported and you will get the below error:

````
[Syntax error in CQL query] message="line 1:253 no viable alternative at input ';' (...< '2000-01-01' and l_commitdate < [l_receiptdate] ;)”>\
````

Cassandra doesn't support having a condition of a column compared to another column within the same table. So I had to use a normal static value (and l_commitdate < '2000-01-01') which not what the original query require. 


Finally, similar to the first query Q1 you can't order by the same column that you are grouping by. So I couldn't order the results by the o_orderpriority as in the original query since this column is the partition key and can't be used as a culturing column. 

Based on our data, the result of the query is as shown below:

````
[Row[1-URGENT, 1], Row[5-LOW, 4]]
````


The complete api function that will get the results of Q4 is shown below:

````

    @GET
    @Path("/q4")
    @Timed
    @ApiOperation(value = "get result of TPCH Q4 using this model", notes = "Returns String", response = String.class)
    @ApiResponses(value = {@ApiResponse(code = 500, message = "internal server error !")})
    public String getQ3Results() {

        try {

            String createIndexOnCommitDate = "CREATE INDEX IF NOT EXISTS l_commitdate_index_on_TPCH_Q4 ON CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 (l_commitdate);";

            this.model.connect();

            ArrayList<Row> rowsResult = new ArrayList<Row>();


            String getAllPartitionKeyValues = "select distinct  o_orderpriority from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 ;";


            ResultSet rsAllPartitionKey = this.model.executeStatement(getAllPartitionKeyValues);

            // get aggregate function per partition
            for (Row row : rsAllPartitionKey.all()) {

                String q4Statement = "SELECT o_orderpriority, count(*) as order_count from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q4 \n" +
                        "where  o_orderpriority ='" + row.getString("o_orderpriority") + "' and o_orderdate >=  '1990-01-01' and o_orderdate < '2000-01-01' and l_commitdate < '2000-01-01' ALLOW FILTERING ";


                ResultSet rsQ4 = this.model.executeStatement(q4Statement);

                for (Row innerRow : rsQ4.all())
                    rowsResult.add(innerRow);
            }


            this.model.executeStatement(createIndexOnCommitDate);

            this.model.close();

            return rowsResult.toString();
        } catch (Exception e) {
            LOGGER.log(Level.SEVERE,
                    "internal server error !" + e.getLocalizedMessage());
            final String shortReason = "internal server error !";
            Exception cause = new IllegalArgumentException(shortReason);
            throw new WebApplicationException(cause,
                    javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
        }
    }

````

















