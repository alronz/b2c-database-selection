


### [Cassandra](../Cassandra.md) > [Query Model](Query Model.md) > Aggregation and Filtering
___


In this section, I will talk about how to filter and aggregate data in Cassandra.



##### Aggregation


The data in Cassandra is grouped by the partition key and the clustering columns. The partition key can be simple (single column) or composite (multiple columns). Clustering columns are used to sort the data within the partition. Choosing the right grouping of your data can impact the read and write performance. For more details on how to choose the partition key and the clustering columns, please have a look to the [data layout section](../Data Model/Data Layout.md).


Additionally, the data can be grouped in Cassandra using the collection data type. Cassandra supports the use of a list, map or a set data types that can be used to group multiple related data together. For example, you can store the address of a customer using the map data type.  It is also possible to use user-defined data types that can be complete objects which can be so useful to store related data together. For instance, you can store the supplier details of a product as a user defined data type inside the product table. For more details about the collection and user defined data types, please have a look to the [data layout section](../Data Model/Data Layout.md).


Cassandra also supports the Tuple data type which can be used to store two or more values together in a column. For example if you want to store the product table which contains the longitude and latitude values. Then you can store it in a single column having two float values as shown below:



````
CREATE TABLE product ( 
id UUID, 
name int, 
location tuple<float,float>,size text, 
release_year text,
price decimal, 
tags map<text,text>, 
PRIMARY KEY (release_year, price) );````


Cassandra has a built-in support for common aggregate functions such as min, max, avg, sum, and count.  Cassandra also supports the creation of custom user defined functions. In this section, I will explain how to use the built-in functions as well as the user defined functions.


Assuming we have created the below order table:

````
CREATE TABLE order (id UUID, items set<text>, total decimal, order_year, status text PRIMARY KEY (order_year,total);
````

Then you can get the sum of all orders totals for the year 2015 as shown below:

````
SELECT sum(total) FROM order WHERE order_year='2015';
````

Or get the average price of all the orders in the year 2015:

````
SELECT avg(total) FROM order WHERE order_year='2015';
````

Or get the min or max order total price for the year 2015:

````
SELECT min(total) FROM order WHERE order_year='2015';
````

````
SELECT max(total) FROM order WHERE order_year='2015';
````

And to get the count of the orders in 2015:

````
SELECT count(*) FROM order WHERE order_year='2015';
````

Finally you can limit your returned results using the LIMIT clause as shown below:

````
SELECT * FROM order WHERE order_year='2015' LIMIT 100;
````

In the above query, we are returning the first 100 results.





Cassandra also supports user defined functions. Assuming we want to get also the TAX for each order. Then we can create a custom user defined function to support this as seen below:


````
CREATE OR REPLACE FUNCTION fTax (input double,taxPercentage int) CALLED ON NULL INPUT RETURNS double LANGUAGE java AS 'return Double.valueOf(input.doubleValue() * taxPercentage);';
````

Then we can run a query as below:


````
SELECT id,fTax(total, 0.06) FROM order WHERE order_year='2015' ;
````

##### Filtering

Filtering data in Cassandra is done by using the WHERE clause where you can query data either by its partition key, clustering columns or secondary indexed columns. The WHERE clause supports these conditional operators: CONTAINS, CONTAINS KEY, IN, =, >, >=, <, and <=.

CONTAIN is used to filter the data based on a certain value in a collection as seen in the below example:

Assuming we have already created the below product table:

````
CREATE TABLE product ( 
 id UUID,
 name int, size text,
 release_year text,
 price decimal, 
 tags set<text>,
 PRIMARY KEY (release_year, price) );````

Then we can check if the product is having a certain tag as below:

````
SELECT * FROM product WHERE tags CONTAINS 'Electronics';
````

Or use CONTAINS KEY in case we are searching a map key.  For example, if we have created the below table:

````
CREATE TABLE product ( 
id UUID,
name int,size text, 
release_year text,
price decimal,
tags map<text,text>,
PRIMARY KEY (release_year, price) );````
Then we can query to check if the map has a value on a certain key as seen below:

````
SELECT * FROM product WHERE tags CONTAINS KEY 'isDeleted';
````








