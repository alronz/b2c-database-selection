


### [Cassandra](../Cassandra.md) > [Query Model](Query Model.md) > Indexing
___

Cassandra supports secondary indexes that are used to access the data using columns other than the primary key columns.  The indexes are built in the background by Cassandra without blocking writes or reads. The indexes provide efficient and fast lookup based on matching conditions for any secondary columns. 

Usually it is a good practice to create indexes on columns that aren't having high-cardinality (having many distinct or unique values). Creating indexes on high-cardinality will introduce many seeks for few results and it is not recommended. For example, it is better to create an index on the products release_year column instead of the product id since the product id has high-cardinality while the release_year has medium-cardinality and can group multiple results. In addition, creating an index on too low-cardinality columns aren't recommended since they don't make much sense. For example, if you create an index on a boolean data type that has only true or false values, then this will result on two huge rows having all the data. Therefore, it is recommended to use columns that doesn't have too high-cardinality or too low-cardinality but are having medium cardinality.

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

Now you can only query the product table using the partition key (id), if you want to query the table using the release_year, then you can create an index on the release_year as shown below:

````
CREATE INDEX ryear ON product (release_year);
````


````
SELECT * from product where id = 2 and release_year = "2015" 
````

In the above example we have provided the product id since it is the partition key and the query will through an error if we don't provide it in the WHERE statement. However if you want to run query against the secondary index without specifying the partition key, then we can use the can use the "ALLOW FILTERING" clause as shown below:

````
SELECT * from product where release_year = "2015" ALLOW FILTERING
````


In the same way, ff you have a composite partition key, then you should provide the values of all the keys in the SELECT statement, otherwise your query will fail. To query using only a single key of the composite partition key, then you need to create an index on this key as shown below:


````
create table product (
      id text,
      name text,
      price decimal,
      release_year text,
      size text,
      PRIMARY KEY((size,release_year))      
  );


// The above query will fail since you need to provide the size in the WHERE statement:

SELECT * from product where release_year = "2015" 


// However you can create an index on the release_year column as shown below:


CREATE INDEX ryear ON product (release_year);

// Then the SELECT query will succeed.

````


It is also possible to create multiple secondary indexes as shown below:

````
CREATE INDEX name ON product ( name );CREATE INDEX price ON product ( price );
````

Then you can run queries on both secondary indexes as below:


````
SELECT * from product where price > 1000 AND name = "Dell" ALLOW FILTERING;
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
CREATE INDEX tags ON product ( tags );
````  
Then you can run queries on the collection column as the below:

````
SELECT * FROM product WHERE tags CONTAINS 'Electronics' ALLOW FILTERING;````  
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
CREATE INDEX address ON product (ENTRIES(address));
````


Then you can run queries against the ma as the below:


````
SELECT * FROM product WHERE address['street'] =  'Breslauer Str. 2' ALLOW FILTERING;````


Finally, you can create an index on a list as below:


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
CREATE INDEX categories ON product (FULL(categories)) ALLOW FILTERING;
````


Then you can run a query against the list like below:


````
SELECT * FROM product WHERE categories =  [1,4,7] ALLOW FILTERING;````






























 