


### [Cassandra](../Cassandra.md) > [Query Model](Query Model.md) > Query Options
___


CQL supports a SELECT statement similar to SQL with many options that can be used to run simple or complex queries in Cassandra. In this section, I will explain how you can query your data in Cassandra using CQL SELECT statement.


SELECT statement in CQL can be used with the WHERE clause to run parameterised or range queries. However in Cassandra, you can use the WHERE statement only with the columns that are either part of the primary key or having secondary indexes. Otherwise, the query will fail. 

Assuming we have created a product table using the statement below:

````
CREATE TABLE product (
 id UUID, 
 name text,
 release_year text,
 price decimal, 
 tags set<text>,
 PRIMARY KEY (release_year, price) );

Then we can run many queries against the product table as shown below:

````
// get all from product
SELECT * FROM product

// Or return only the product name
SELECT name FROM product
SELECT * FROM product WHERE release_year = '2015'
SELECT * FROM product WHERE release_year = '2015' AND price > 1000

// You can also return the results in a JSON format 
SELECT JSON FROM product WHERE release_year = '2015' AND price > 1000

SELECT * FROM product WHERE release_year IN ('2015' , '2014') 


````
````

````
````

````

To query all available user defined aggregates functions:

````
