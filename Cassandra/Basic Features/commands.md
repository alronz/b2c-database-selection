#### [back](basic_features_main.md)



Cassandra supports a SQL-simlar query language called CQL (Cassandra Query Language) that can be used to interact with the stored data. CQL can be accessed using a shell script called cqlsh which is found inside the installation folder in the below path :

````
bin/sh cqlsh
````

Then you will be connected as shown below:

![image](https://s3.amazonaws.com/b2cbucket/cqlsh.png)


When you run cqlsh command, you will be connected by default to your local node. If you want to connect to another node, you can run the command as shown below:


````
bin/sh cqlsh 1.2.3.4 9042
````

The "1.2.3.4 9042" in the above example is the IP address and port number of the node that you want to connect to. 

In the following sections, I will explain briefly how you can read and write data in Cassandra using CQL.


##### Reading data

Querying data in CQL is done using the "SELECT" statement in a similar way like we do in SQL.
The mean difference in the SELECT statement between SQL and CQL is that in CQL you can only query the data using the columns of the primary key. Otherwise Cassandra will reject the query. For example, if you want to query the product table using the id column, then you need to define the id column to be part of the primary key as shown below:

````CREATE TABLE ecommerce.product ( id UUID, name text, added_date timestamp, price decimal, weight text, size text, PRIMARY KEY (id) )
````

Now since the id column is part of the primary key, then you can run queries against it as shown below:

````
SELECT * FROM ecommerce.product WHERE id =2
````

You can also run queries against columns that aren't part of the primary key of the table if you create a secondary index on them. More about primary key and indexes will be explained in the [data layout](../Data Modeling/data_layout.md) section and the [indexes section](../Search Data/index.md). 

Querying the data will be explained in more details in the [searching section](../Search Data/query.md).


##### Writing data


For each application, we would need at first to create a Keyspace that will contain our tables which acts like a schema in a relational database. For creating a Keyspace, you can use the "CREATE KEYSPACE" statement as shown below:

````
CREATE KEYSPACE ecommerce WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy'[ 'datacenter1' : 3 ] }; ````
For each Keyspace, you can define some attributes as shown above. We have created a keyspace called ecommerce using the "NetworkTopologyStrategy" replication strategy. 
Later if you want to change the keyspace attributes, you can use the "ALTER KEYSPACE" clause as shown below:

````
ALTER KEYSPACE "ecommerce" WITH REPLICATION =  { 'class' : 'SimpleStrategy', 'replication_factor' : 2 };````
To drop the keyspace, you can use the "DROP" clause as shown below:
````DROP KEYSPACE ecommerce;
````

To create a CQL table inside the "ecommerce" keyspace, we can use the "CREATE TABLE" statement using the "WITH" clause to assign table properties as seen below:
````CREATE TABLE ecommerce.product ( id UUID PRIMARY KEY, name text, added_date timestamp, price decimal, weight text, size text ) WITH compaction =
    { 'class' : 'SizeTieredCompactionStrategy'}; 
````

Above we have created a table having the id as the primary key. Enforcing a schema as seen above is optional but it is also recommended to know what are the data stored in each table. Unlike the relational databases, if you don't want to store a value in a certain column, then nothing will be stored instead of storing null. This means you can have rows having only name and price, while others having also weight and size. 


Later if you wish to change the table structure such as changing the data type of a particular column, then you can use the "ALTER TABLE" statement as shown below:

````
ALTER TABLE ecommerce.product ADD color text;
````

You can also create a materialised views in Cassandra using the "CREATE MATERIALIZED VIEW" statement as seen below:


````
CREATE MATERIALIZED VIEW ecommerce.product_MVAS SELECT * FROM ecommerce.productWHERE price > 20 AND size < 50PRIMARY KEY (id);````
To drop a table or a materialised view, you can use the "DROP TABLE" or "DROP MATERIALIZED VIEW" clauses as shown below:
````DROP TABLE ecommerce.product;
````
````DROP TABLE ecommerce.product_MV;
````


To insert data into the table, you can use the "INSERT INTO" statement as shown below:

````
INSERT INTO ecommerce.product ( name, price,size ) VALUES ('Dell Laptop', 736.3, 15);
````

Similarly you can run the "UPDATE" statement to update the data as seen below:

````
UPDATE ecommerce.product SET weight = 20 WHERE id = "2";
````

And to delete the data, the "DELETE" statement can be used. Unlike SQL, you can delete columns or even values from a columns with a collection data type, example is shown below:

````
DELETE size FROM ecommerce.product WHERE id IN (1,2,3) 
````

It is also possible to delete entire rows using just "DELETE" without specifying any columns as shown below:

````
DELETE FROM ecommerce.product WHERE price = 0;
````









































 