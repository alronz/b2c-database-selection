[Home](../../index.md)

[Cassandra Content](../Cassandra.md)
___

# Cassandra > Query Model > Query Options


CQL supports a SELECT statement similar to SQL with many options that can be used to run simple or complex queries in Cassandra. In this section, I will explain how you can query your data in Cassandra using CQL SELECT statement.


SELECT statement in CQL can be used with the WHERE clause to run parameterised or range queries. However in Cassandra, you can use the WHERE statement only with the columns that are either part of the primary key or having secondary indexes. Otherwise, the query will fail. 

Assuming we have created a product table using the statement below:

````
CREATE TABLE product (
 id UUID, 
 name text, size text, 
 release_year text,
 price decimal, 
 tags set<text>,
 PRIMARY KEY (release_year, price) );````

Then we can run many queries against the product table as shown below:

````
// get all from product
SELECT * FROM product

// Or return only the product name
SELECT name FROM product// Or query using the partition key
SELECT * FROM product WHERE release_year = '2015'// or run range queries using the clustering column:
SELECT * FROM product WHERE release_year = '2015' AND price > 1000

// You can also return the results in a JSON format 
SELECT JSON FROM product WHERE release_year = '2015' AND price > 1000````CQL SELECT statement supports also an IN statement that can be used to run queries like below:
````
SELECT * FROM product WHERE release_year IN ('2015' , '2014') ````
Cassandra supports querying some system tables that can be used to get information about keyspaces, tables and the available user-defined data types and functions. You can query these tables whenever you want to know about the current data model. Example is shown below:
To get information about the available keyspaces:````SELECT * FROM system.schema_keyspaces;
````To get information about the available tables:````SELECT * FROM system.schema_columnfamilies 
````To search for table columns, you can query the below system table:
````SELECT * FROM system.schema_columns
````To get the current cluster informations:````SELECT * FROM system.peers;
````To query the available user defined functions:
````SELECT * FROM system.schema_functions;
````

To query all available user defined aggregates functions:
````SELECT * FROM system.schema_aggregates;
````To query all available user defined data types:
````SELECT * FROM system.schema_usertypes;````