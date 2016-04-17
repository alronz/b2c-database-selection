

### [Cassandra](../Cassandra.md) > [Data Model](Data Model.md) > Data Layout
___

Modelling your data in Cassandra is probably one of the most difficult tasks especially if you come from a relational database background. Before talking about how you can model your data in Cassandra and what are the best principles that we need to follow to come up with an efficient data model, I will talk about very important Cassandra concepts that impact your data model design.


##### Partition Key

In Cassandra the data is divided into different partitions and then distributed across the nodes for scalability. Each partition will have a portion of the data and will be stored as a map having different columns keys and values and this map is accessed by a row key called a partition key. Later if any node receives a read request from a client application, it will easily reroute the request to the node having this partition by simply checking which node holds this partition key. It is important that we understand how Cassandra query the data using partition key since this will impact our data model design. We define which column key will be used to be the partition key during the creation of the CQL table. You can do that by using the "PRIMARY KEY" statement. You can either use one column to create a single partition key or more than one column to create a composite partition key. An example is shown below to create a single or composite partition key:


````
create table product_by_id (
      id text,
      name text,
      price decimal,
      size text,
      PRIMARY KEY(id)      
  );
````

Or to create a composite key using id and name columns:


````
create table product_by_id_name (
      id text,
      name text,
      price decimal,
      size text,
      PRIMARY KEY((id,name))      
  );
````


In the above example, you should query your product table by using both partition keys columns (id,name). For instance, if you run a query against the product id only, the query will fail since you need to provide the product name as well. So the below querY will fail:
 


````
SELECT * from product_by_id_name where id = 1
````

But the below query will succeed:


````
SELECT * from product_by_id_name where id =1 and name = "product_name" 
````

As seen above, choosing the right partition key is so important to have a data model that will cover your query pattern. However, it is always possible to create secondary indexes on the fields that we want to query. More about creating indexes will be explained on a [later section](../Query Model/Indexing.md).


The partition key should be chosen so that it can be used to distribute the data equally across the nodes. If you choose the partition key to be a boolean value where it can be either true or false, then you will have only two large partitions that can impact scalability. Additionally, Cassandra has a limit on the data that can be written to a particular partition which is currently around 2 billion rows. In the same way, if you choose the partition key to be a so unique such as a UUID column, then each partition will contain only one row and the ordering will have no value.  Therefore, it is so important to choose the partition key so carefully to be not too high or too low. Besides if you choose a wrong partition key, it can results on an unbalanced partitions which leads to hot spot issues. For example if the partition key is the product category, then we might have partitions having many products while other partitions having only few products. Hence the partitions that contain many products will result on a hot spot nodes that receives most of the read and write request which impacts scalability.




##### Clustering columns

In Cassandra, you can also define clustering columns which can be used if you want to sort your data. To define a clustering columns, you need to add them to the PRIMARY KEY definition after the partition key definition, an example is shown below:


````
create table product_by_release_Year_and_size (
      id text,
      name text,
      release_year text,
      price decimal,
      size text,
      PRIMARY KEY((release_year,size), price )      
  );
````

As seen above, we have defined our partition key to be a composite key containing the release_year and size columns. Then we have used the price column to be the clustering column that we will be used for sorting the product by their price inside each partition. This mean that products will be stored as partitions for each release_year and size values. Then inside each partition, the products will be sorted by the price.  The above primary key is called a compound primary key which contains a composite partition key and a clustering column. 

Now you can run queries to get the products released in a particular year and with a particular size sorted by their prices as seen below:


````
SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large ORDER BY price DESC LIMIT 50;
````

You can also run queries without the clustering columns like below:


````
SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large
````

Additionally, you can run parameterized or range queries against the clustering column as shown below:


````
SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large and price = 1000

SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large and price > 1000
````

Choosing the right clustering columns is so important in your data model design and needs to be done with care. For example the order of the clustering columns is so important since they are used for grouping the data. For instance, if you want to use two clustering columns for the product table like the price and color. Then the order of the clustering columns are (price,color), then your products will be grouped and sorted first by the price and then for each price, it will be grouped and sorted by color.  

If you have used more than one column for clustering, then you can run range queries only on the last clustering column after you specify the values of the preceding clustering columns. For example, if we have created the below table that has (price,color) clustering columns:


````
create table product_by_release_Year_and_size (
      id text,
      name text,
      release_year text,
      price decimal,
      size text,
      PRIMARY KEY((release_year,size), price, color )      
  );
````

Then you can run the below query:

````
SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large and price = 1000 and color > 20
````

But the below two queries will fail:


````
SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large and price > 1000 and color > 20

SELECT * from product_by_release_Year_and_size where release_year =2015 and size = large and  color > 20
````



##### User-Defined and collection data types

Cassandra gives you the ability to create your own custom data type which can be used to embed objects in your data model. For example, if you have a customer table you might be interested to store the address as a separate custom data type field as shown below:


````
CREATE TYPE address (
  street text,
  city text,
  zip_code int,
  phones set<text>
)
````

Then you can create the customer table as shown below:


````
CREATE TABLE customer (
  name text,
  address address,
)
````


Cassandra also supports the use of collection data types such as lists, sets or maps that can contain either a normal or a custom data types. 

Using Cassandra's user-defined and collection data types can impact your data model design by embedding related data from other tables since there is no support for joins in Cassandra.



##### Data modelling principles

Data modelling design in Cassandra is very different from the relational databases. The first rule for good data modelling design in Cassandra is to forget everything we know about relational database data modelling techniques. Although in Cassandra we use similar terminologies such as tables, columns and rows. However, the closest thing to Cassandra data models are maps and not tables. The data in Cassandra is stored as map of maps where each row is like a map that we access by using the partition key.  Below I will talk about few principles that we need to follow to come up with an efficient data model in Cassandra. 


###### Data model design goals

One of the most important goals that we should keep in mind when designing our data model is how to distribute the data evenly across the different nodes. The distribution of the data across the different nodes is done by our partition key choice. For example, If you choose the partition key for the product table to be the product release year.  Then you might end up with partitions having different sizes since maybe in some years more products have been produced than in other years. This results in an unbalanced partitions which leads to more load on certain nodes in the cluster. This mean we should pick up a very good partition key. 

Another important design goal is how to minimise the number of reads. This is also decided based on the partition key. If you have each partition resides in a different node, then we will have to do many reads to get our data which impacts the performance. This mean we should also choose a partition key that will results in partitions containing relatively large amount of related data to minimise the reads between different nodes.


###### Data model design rules

After keeping the above two design goals in mind, we can start designing our data model. Below I WILL explain the two most important design rules that we should keep in mind while designing our data model.

First rule is that in Cassandra we design our data model around the query patterns and not around the entities, objects or the relationships that we have. This is relatively difficult if you don't know in details all the queries that you need in a new application that you are creating. However, it is always possible to think of the possible query patterns based on the functions or use cases that your application is planning to have. Also you might be able to guess the queries from your experience in the application domain. For example, in an B2C ecommerce application, you can guess that we will have products, orders, customer, supplier and categories and you can guess that an important queries would be to get all products under a specific category, get most sold products based on the completed order , etc ..   


The second rule is that it is absolutely to use denormalisation heavily if needed.  Writes in Cassandra are cheap and since joins aren't supported, we would need to do a lot of denormalisation to retrieve our data efficiently and improve performance.


Based on the above two design rules, I will show below a simple example:


Assuming we want to build a data model for a B2C application that serves the below two queries in a product and categories tables:

Q1) get a product per id
Q2) get a category per cat_id
Q3) get all products under a particular category
Q4) get the categories of a particular product


For the first query, we can create a table like below that can be used to serve only this query:



````
create table product_by_id (
      id text,
      name text,
      price decimal,
      size text,
      PRIMARY KEY(id)      
  );
````

Then you can easily run queries as below:


````
SELECT * from product_by_id where id =1
````

For the second query, we can create another table that will serve this query as seen below:


````
create table category_by_id (
      id text,
      name text,
      PRIMARY KEY(id)      
  );
````

Then you can easily run queries as below:


````
SELECT * from category_by_id where id =1
````

For the third query and since joins aren't supported, we should create a table like below:


````
create table all_products_by_cat_id (
      cat_id text,
      prod_id text,
      name text,
      price decimal,
      size text,
      PRIMARY KEY(cat_id,prod_id )      
  );
````

Then you can easily run queries as below:


````
SELECT * from all_products_by_cat_id where cat_id =1
````


And for the last query, we redesign the product_by_id table so that it will store the category names as a set as shown below:



````
create table product_by_id (
      id text,
      name text,
      cat set<text>
      price decimal,
      size text,
      PRIMARY KEY(id)      
  );
````

Then you can easily run queries as below:


````
SELECT cat from product_by_id where id =1
````



As seen above we create a table for each query that we are planning to run later in our application even if that means so much data redundancy. We need also to think if this query is going to be run frequently or not and then think if it worths creating a separate table for it to improve the performance. 

It is difficult to give strict rules when designing data model in Cassandra since you can come up with many different data models. So you need to think which design will be suitable for your application requirements. 



