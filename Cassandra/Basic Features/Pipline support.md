#### [back](basic_features_main.md)


To import or export data from or to Cassandra, CQL provides a very easy to use clause called "COPY" that can be used to either import or export data from a table and into or from a CSV file. The "COPY" command can be used with the below syntax:

To import data: 

````
COPY table_name ( column, ...)
FROM ( 'file_name' | STDIN )
WITH option = 'value' AND ...
````

To Export data:

````
COPY table_name ( column , ... )
TO ( 'file_name' | STDOUT )
WITH option = 'value' AND ...
````

There are many options that you can use in the statements above such as the options related to the CSV file format or to specify many other options such as CHUNKSIZE, MAXBATCHSIZE, MAXROWS, and SKIPROWS. 

An example is given below to show how to import and export data from the product table:


To import data into the product table:

````
COPY ecommerce.product
(name, price, size, weight)
 FROM 'product.csv'
 WITH DELIMITER='|'
````

Or to export data from the product and into the product.csv file:

````
COPY ecommerce.product
(name, price, size, weight)
 TO 'product.csv'
 WITH DELIMITER='|'
````


In addition, Cassandra supports a shell command called SOURCE which allow you to execute multiple CQL statement from a file. An example is show below:


````
SOURCE ./file_containing_cql_statements
````