[Home](../../index.md)

[Cassandra Content](../Cassandra.md)
___

# Cassandra > Query Model > Sorting


Cassandra supports sorting using the clustering columns. When you create a table, you can define  clustering columns which will be used to sort the data inside each partition in either ascending or descending orders. Then you can easily use the ORDER BY clause with the ASC or DESC options. An example is given blow:

Assuming we have created the below table:

````
CREATE TABLE product (
 id UUID, 
 name text, size text, 
 release_year text,
 price decimal, 
 tags set<text>, 
 PRIMARY KEY (release_year, price) ) WITH CLUSTERING ORDER BY (price DESC);````

Notice that we used the price column to be the clustering column and defined the default sorting order to be DESC using the "WITH CLUSTERING ORDER BY" clause. If we didn't use the "WITH CLUSTERING ORDER BY", the default order is ascending. Now if we run a query against the product table, we will get the data sorted descendingly by the price in each partition.


````
SELECT * FROM product  where release_year = 2015
````

Or to sort the data in an ascending order, we can run the below query:

````
SELECT * FROM product  where release_year = 2015
ORDER BY price ASC
````

