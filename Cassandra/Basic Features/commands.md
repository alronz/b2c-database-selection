#### [back](basic_features_main.md)




Cassandra uses CQL to interact with the stored data, CQL is a query language which has a syntax similar to the SQL language used in relational databases. To access CQL, you can use the CQL shell cqlsh that can be found inside the installation folder in the below path :

````
bin/cqlsh
````

If you run cqlsh, it will connect to the local node. If you want to connect to another node, then you can run it as below:


````
bin/cqlsh 1.2.3.4 9042
````

Where 1.2.3.4 9042 is the IP address and port of the node you want to access. 

Below I will explain briefly how you can read and write data to Cassandra using CQL.


##### Creating data using CQL


First you might want to start by creating a keyspace using the "CREATE KEYSPACE IF NOT EXISTS" clause as seen below:

````
CREATE KEYSPACE IF NOT EXISTS ecommerce WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 3 }; ````
 
 As seen above, we have created a keyspace called ecommerce with replication factor 3 inside datacenter1 and using the NetworkTopologyStrategy replication strategy.
 
Later if you want to change the keyspace attributes, you can use the "ALTER KEYSPACE" clause as seen below:

````
ALTER KEYSPACE "ecommerce" WITH REPLICATION =  { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };````
To drop the keyspace, you can use the "DROP" clause as shown below:
````DROP KEYSPACE ecommerce;
````

To create a CQL table inside the "ecommerce" keyspace, we can use the "CREATE TABLE" callus and using "WITH" to assign any table properties as seen below:
````CREATE TABLE ecommerce.product ( id UUID PRIMARY KEY, name text, added_date timestamp, price decimal, weight text, size text ) WITH compaction =
    { 'class' : 'SizeTieredCompactionStrategy'}; 
````

Above we have created a table having the id as the partition as primary key. You can also use a composite partition key having more than one column. Enforcing a schema as seen above is optional but it is also recommended to know what are the data stored in each table. Unlike the relational databases, if you don't want to store a value in a certain column, then nothing will be stored instead of storing null. This means you can have rows having only name and price, while others having also weight and size. 


Later if you wish to change the table structure such as changing the data type of a particular column, then you can use the "ALTER TABLE" clause as shown below:

````
ALTER TABLE ecommerce.product ADD color text;
````

You can also create a materialised views in cassandra using the "CREATE MATERIALIZED VIEW" clause as seen below:


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

Similarly you can run the "UPDATE" clause to update the data as seen below:

````
UPDATE ecommerce.product SET weight = 20 WHERE name = "Tshirt" AND price = 302;
````

And to delete the data, the "DELETE" clause and unlike sql, you can delete columns or even values from a columns with a collection data type, example is shown below to remove the size from all products having name as "Dell Laptop":

````
DELETE size FROM ecommerce.product WHERE name = "Dell Laptop"
````

It is also possible to delete entire rows using just "DELETE" without specifying any columns as shown below:

````
DELETE FROM ecommerce.product WHERE price = 0;
````

Finally to retrieve data, you can use the "SELECT" clause in a similar way like we do using sql. An example is shown below:

````
SELECT * FROM ecommerce.product WHERE price > 200;
````

An important note is that you can only query the columns that are part either part of partition keys or the clustering columns. Otherwise Cassandra will reject the query.  For example, if you want to query using the price as seen above, you need to add it to the either the composite or compound keys as shown below:


````CREATE TABLE ecommerce.product ( id UUID, name text, added_date timestamp, price decimal, weight text, size text, PRIMARY KEY (id, price) )
````

In the above statement, we have create the product table having the id as the partition key and both the id and the price as a compound key that can be used to sort the data based on this compound key. After creating the table using as shown above, then you can run queries against the price column.








































 