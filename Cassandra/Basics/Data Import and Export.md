


### [Cassandra](../Cassandra.md) > [Basics](Basics.md) > Data Import and Export
___

To import or export data from or into Cassandra, CQL provides a simple statement called "COPY" that can be used to either import or export data. The COPY statement can either export data from a table into a CSV file or import data from a CSV file and into a table. The "COPY" command can be used with the below syntax:

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

There are many options that can be specified with the above commands such as CHUNKSIZE, MAXBATCHSIZE, MAXROWS, or SKIPROWS.  For more details about all the options, please have a look to the [documentations](http://docs.datastax.com/en//cql/3.1/cql/cql_reference/copy_r.html).

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


In addition, Cassandra supports a shell command called SOURCE which allows you to execute multiple CQL statement from a file. An example is show below:


````
SOURCE ./file_containing_cql_statements
````