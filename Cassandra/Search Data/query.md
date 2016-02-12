#### [back](search_data_main.md)


CQL supports a SELECT clause similar to SQL with many options that can be used to run simple or complex queries in Cassandra. In this section, I will explain how you can query your data in Cassandra using CQL SELECT statement.


SELECT clause in CQL can be used with the WHERE clause to run simple or range queries. However I should highlight the fact that in Cassandra, you can use the WHERE statement with columns that are either part of your partition key, clustered columns or columns with secondary indexes. Otherwise, the query will fail. 

Assuming we have created a product table with the statement below:

````
CREATE TABLE product ( id UUID, name text, size text, release_year text,price decimal, tags set<text>, PRIMARY KEY (release_year, price) );````

Then we can run queries to get all the data in the product table using below query:

````
SELECT * FROM product````

Or you can return only the product name using the below query:

````
SELECT name FROM product````To get all the products that were released on a certain year, then we can use the below query:
````
SELECT * FROM product WHERE release_year = '2015'````
As you can see above, we used a partition key in the WHERE clause. We can also run a range query as seen below:
````
SELECT * FROM product WHERE release_year = '2015' AND price > 1000````

You can also return the results in a JSON format as seen below:

````
SELECT JSON FROM product WHERE release_year = '2015' AND price > 1000````
Beside supporting the AND clauses in a similar way like SQL, CQL SELECT statement supports also an IN clause. Using the IN clause you can run queries like below:
````
SELECT * FROM product WHERE release_year IN ('2015' , '2014') ````
Cassandra has some system tables that can be used to get information about keyspaces, tables and user-defined data types and functions. You can query these tables whenever you want to know about the current data model. Example is shown below:
To get information about available keyspaces:````SELECT * FROM system.schema_keyspaces;
````To get information about available tables:````SELECT * FROM system.schema_columnfamilies 
````To search for table columns , you can query the below system table:
````SELECT * FROM system.schema_columns
````To get current cluster informations:````SELECT * FROM system.peers;
````To query the available user defined functions:
````SELECT * FROM system.schema_functions;
````

To query all available user defined aggregates functions:
````SELECT * FROM system.schema_aggregates;
````To query all available user defined data types:
````SELECT * FROM system.schema_usertypes;````

