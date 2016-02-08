#### [back](search_data_main.md)

Cassandra supports secondary indexes that are used to access the data using columns other than the partition key.  The indexes are built in the background by Cassandra without blocking writes or reads. The indexes provide efficient and fast lookup based on matching conditions for any secondary columns. 

Usually it is a good practice to create indexes on columns that aren't having high-cardinality which are the columns that are having many distinct or unique values. Creating indexes on high-cardinality will introduce many seeks for few results and it is not recommended. For example, it is better to create an index on the products release_year column instead of the product id since the product id has high-cardinality while the release_year column can group more results. In addition, creating an index on low-cardinality columns aren't recommended since they don't make much sense. For example, if you create an index on a boolean data type that has only true or false values will result on two huge rows having all the data having either false or true. Therefore, it is recommended to use columns that doesn't have high-cardinality or low-cardinality but are having medium cardinality.

The other guidance principle that we should consider when creating indexes is to avoid creating indexes on columns that have frequent delete operations. The reason for that is that Cassandra stores up to 100K tombstones in the index, then the queries on the indexed columns will fail.



In Cassandra, you can create an index on any secondary column of a table after the table definition statement. For example, we can create an index on the product release_year as shown below:



````
create table product (
      id text,
      name text,
      price decimal,
      release_year text,
      size text,
      PRIMARY KEY(id)      
  );
````

Now you can only query the product table using the partition key (id), if you want to query the table using the release_year, then you can create an index on the release_year:


````
SELECT * from product where release_year = "2015" 
````

You will get an error if you run the above query since you haven't provided the value of the partition key (id column). However to avoid getting this error, you can use the "ALLOW FILTERING" clause as shown below:



````
SELECT * from product where release_year = "2015" ALLOW FILTERING
````


If you have a partition key that contains a composite key, then you should provide the values of all the keys in the composite key otherwise your query will fail. To query using only a single key of the composite key, then you need to create an index on this key as shown below:

````
create table product (
      id text,
      name text,
      price decimal,
      release_year text,
      size text,
      PRIMARY KEY((size,release_year))      
  );
````



````
SELECT * from product where release_year = "2015" 
````

The above query will fail since you need to provide also the size column value on the condition. However you can create the below index :

````
CREATE INDEX ryear ON product (release_year);
````

Then the below query will succeed :

````
SELECT * from product where release_year = "2015" 
````




It is also possible to create multiple secondary indexes as shown below:

````
CREATE INDEX name ON product ( name );CREATE INDEX price ON product ( price );
````

Then you can run queries on both secondary indexes as below:


````
SELECT * from product where price > 1000 AND name = "Dell"  ALLOW FILTERING
````



Finally, you can create indexes on a collection such as a list, a map or a set as shown below:



````
create table product (
      id text,
      name text,
      price decimal,
      tags set<text>,
      release_year text,
      size text,
      PRIMARY KEY((size,release_year))      
  );
````


````
CREATE INDEX tags ON product ( tags );
```` 
 
Then you can run queries such as the below:


````
SELECT * FROM product WHERE tags CONTAINS 'Electronics';```` 
 
Or you create an index on a map like below:

 

````
create table product (
      id text,
      name text,
      price decimal,
      tags set<text>,
      address map<text,text>,
      release_year text,
      size text,
      PRIMARY KEY((size,release_year))      
  );
````



````
CREATE INDEX address ON product (ENTRIES(address));
````


Then you can run queries such as the below:


````
SELECT * FROM product WHERE address['street'] =  'Breslauer Str. 2';````


Or you can create an index on a list as below:



````
create table product (
      id text,
      name text,
      price decimal,
      tags set<text>,
      address map<text,text>,
      categories  List<int>,
      release_year text,
      size text,
      PRIMARY KEY((size,release_year))      
  );
````

````
CREATE INDEX categories ON product (FULL(categories));
````


Then you can run a query like below:


````
SELECT * FROM product WHERE categories =  [1,4,7];````






























 