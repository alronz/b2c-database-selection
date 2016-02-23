#### [back](search_data_main.md)


In this section, I will talk about how to filter and group data in Cassandra.


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


##### Grouping


The data in Cassandra is grouped by the partition key and the clustering columns. The partition key can be simple (single column) or composite (multiple columns). Clustering columns are used to sort the data within the partition. Choosing the right grouping of your data can impact the read and write performance. For more details on how to choose the partition key and the clustering columns, please have a look to the [data layout section](../Data Modeling/data_layout.md).


Additionally, the data can be grouped in Cassandra using the collection data type. Cassandra supports the use of a list, map or a set data types that can be used to group multiple related data together. For example, you can store the address of a customer using the map data type.  It is also possible to use user-defined data types that can be complete objects which can be so useful to store related data together. For instance, you can store the supplier details of a product as a user defined data type inside the product table. For more details about the collection and user defined data types, please have a look to the [data layout section](../Data Modeling/data_layout.md).


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







