#### [back](search_data_main.md)


Cassandra supports sorting using the clustered columns. When you create a table, you can define the clustering columns which will be used to sort the data inside a partition in an ascending or descending order. Then you can easily use the ORDER BY clause with the ASC or DESC options. An example is given blow:

Assuming we have created the below table:

````
CREATE TABLE product ( id UUID, name text, size text, release_year text,price decimal, tags set<text>, PRIMARY KEY (release_year, price) 
WITH CLUSTERING ORDER BY (price DESC););````

Notice that we used the price column to be the clustering column and defined the default sorting order to be DESC using the "WITH CLUSTERING ORDER BY" clause, then we can sort the data using the price as shown below:


````
SELECT * FROM product  where release_year = 2015
ORDER BY price
````

Or to sort the data in an ascending order, we can run the below query:

````
SELECT * FROM product  where release_year = 2015
ORDER BY price ASC
````