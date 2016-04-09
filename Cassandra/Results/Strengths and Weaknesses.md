[Home](../../index.md)

[Cassandra Content](../Cassandra.md)
___

# Cassandra > Results > Strengths and Weaknesses

In this section, I will talk about the strengths and weaknesses of Cassandra and when using Cassandra makes sense.


##### Strength

Cassandra has many strengths that makes it the perfect choice for many uses cases , below I talk briefly about them.

###### Performance

Like most NoSQL databases, Cassandra comes with all the high performance benefits that other NoSQL debases can give.  Cassandra provides great performance under large data sets and based on the End Point Benchmark for top NoSQL databases, Cassandra outperformed the other NoSQL databases in both throughput and latency. Cassandra is also designed for heavy writes where any insert or update will be written immediately without locking or reading existing data to check for constraints violation which makes writes very fast. Updates are also very fats and called upserts since a new data will be written with a different timestamp. Later a repair process will be run periodically to check for constraints violations and to merge the data and create the final data set.

###### Scalability


Cassandra supports linear and elastic scalability due to its distributed architecture. Linear scalability means that the capacity of the read/write throughputs is increased by simple adding or removing nodes to the cluster. Elastic scalability means that you can easily scale  up or down by just adding or removing nodes. Adding and deleting the nodes is smooth and will happen without any disturbance.  When a new node is added, it will get automatically an even portion of the data from other nodes. In the same way, if a node is removed or failed, the data and the load that was assigned to the node will be distributed evenly to the other nodes in the cluster. 


###### Architecture

Cassandra is built as a peer-to-peer distributed database where all the node are equally important with no master or slave and has no single point of failure. Besides, having equally important nodes makes the architecture more robust where any node can accept read/write requests from the clients and hence Cassandra can provide better support for features such as scalability and availability.



###### Fault Tolerance & Availability


Since Cassandra is using the distributed architecture where all nodes are equal, Cassandra has no single point of failure and multiple nodes can fail without impacting the overall availability of the database. If a node failed, any other node can still be able to receive requests from the client and give back the results.  Cassandra has a multi datacenter support where nodes can span multiple data centres in different geographical locations which also improves the availability and fault tolerance of the database. Finally, Cassandra supports replication which stores duplicate copies of each written data. The replication is even done across data centres which means that even if a complete data centre is down for any reason, the data can be still safely replicated in other data centres.


###### Consistency


Cassandra can be configured to have either eventual consistency or a strong consistency since it supports what is called a tunable consistency.  The consistency level is determined by the number of replicas that should acknowledge the write before it is considered as successful.  Cassandra is optimised to be AP system (highly available and partition tolerant) based on the CAP theorem that states that it is possible for a system to have only 2 out of the 3 features (Consistency, availability and partition tolerance). Hence Cassandra is usually configured to be eventual consistence. If you configure Cassandra to be strong consistent, you might impact the availability of the system based on the CAP theorem.



##### Weaknesses

Below are the main weakness of Cassandra:


###### Query

Cassandra has a multiple weakness when it comes to querying the data. Below I will explain the main query weaknesses:

- No joins or subquery support. If you have relational data and you want to run joins across them, then you would need to duplicate your data in Cassandra. Denormalising your data is encouraged by Cassandra to achieve better performance and it is the only way to be able to support relational queries such as complex joins. Denormalising your data is not so bad but it will require you to maintain the duplicated data manually with each update or insert which could be messy sometimes. Generally, you create a separate table for each query which means you would have to maintain these different tables each time a new data is inserted or updated.

- No ad-hoc query support. As explained previously, you need to design your data model based on the query patterns. This means for each query, you need to think beforehand how to model it in a separate table. Then this table can serve efficiently only the queries that it was built for. This means you can't run ad-hoc queries.


- Range queries on partition key are not supported. Range queries on Cassandra can be done only on columns that are part of the clustering column (with conditions that will be explained later) or columns that have secondary indexes created on them. However if you have chosen a certain column to be part of the partition key, then you can't run range queries on it even if you create secondary index on it. You will get the below error if you try to run range queries on a partition key:

````
Only EQ and IN relation are supported on the partition key
````


-  If you want to run range query on a clustering column, then the preceding clustering column needs to be specified in the query. An example is shown below:


````
// assuming we have the below table 
 CREATE TABLE IF NOT EXISTS CASSANDRA_EXAMPLE_KEYSPACE.Lineitem
(
orderkey text,
linenumber text,
o_orderdate timestamp,
o_shippriority text,
c_mktsegment text,
l_extendedprice double,
l_discount double,
l_shipdate timestamp,
PRIMARY KEY ((orderkey,linenumber),o_orderdate,l_shipdate)
);

// as you can see the clustering columns are o_orderdate,l_shipdate
// if we run the below query
select * from CASSANDRA_EXAMPLE_KEYSPACE.Lineitem where orderkey = '2' and linenumber = '1'  and  l_shipdate > '1990-01-01';

// this will fail with the below error

// "Clustering column "l_shipdate" cannot be restricted (preceding column "o_orderdate" is restricted by a non-EQ relation)"

// this mean, the o_orderdate need to be specified, the below query will succeed

select * from CASSANDRA_EXAMPLE_KEYSPACE.TPCH_Q3 where orderkey = '2' and linenumber = '1'  and o_orderdate = '1996-01-01' and l_shipdate > '1990-01-01'; 
````


- When you run a query, it is either you run it across all the partitions or you run it on a particular partition. If you run it on a particular partition then you need to specify all the columns values that are part of the partition key as shown below:


````
// assuming the table in the previous example 

select * from CASSANDRA_EXAMPLE_KEYSPACE.Lineitem where orderkey = '2' and linenumber = '1' 

// if you specify only the orderkey value in the above query, then you will get the error below 
// "Partition key parts: orderkey, linenumber must be restricted as other parts are"

````


- Running queries that compare the values of two columns is not supported. An example is below:


````
// assuming the table in the previous example 

select * from CASSANDRA_EXAMPLE_KEYSPACE.Lineitem where orderkey = '2' and linenumber = '1' 
and o_orderdate < l_shipdate

// this will through the error below:
// "[Syntax error in CQL query] message="line 1:253 no viable alternative at input "

````

- no Regexp support. No queries like “select * from table where column like “%key%””.

- Searching capabilities such as full-text search isn't supported internally but external plugins such as solr can be used.  


###### Aggregation

Cassandra has some weakness when it comes to data aggregation as will explained below:

-  Aggregation is on the partition level. If you want to run aggregate queries on Cassandra then you need to specify the partition key since the aggregation will happen on the partition level. Usually, aggregation are more useful if they can be run against the whole database. To do that, you might first need to get the all the partition keys, then run the aggregate query against each partition separately and then aggregate the results.

- Materialised views doesn't support user defined function. Cassandra supports some built-in aggregate functions such as sum, avg, min, max and count. Besides it supports the use of a user defined functions that can be used to do a manipulation for the data before aggregation such as doing some mathematical operations. However, the user defined functions aren't supported if you want to create a materialised view. 


###### Sorting


- Cann't sort by a partition key column. In Cassandra it is not possible to group and order by the same column. Usually we group by a column if we want to do aggregation query. In Cassandra, aggregation is done in the partition level so we group by the partition key. However if you want to run an aggregation query across the database (across all partitions) and later sort the results by the "grouped by" column (the partition key in case of Cassandra), then this is not supported since the sorting is done only by the clustering columns in Cassandra.

- Sorting order is specified at the table creation time. Sorting in Cassandra is done using the clustering columns which are specified at the table creation time. This mean you can't sort by a columns that was returned from a query as we do using SQL. 

- The ORDER BY clause can be used only when the partition key values are specified because the sorting is on the partition level (no sorting across database).

###### Storage

In terms of storage, Cassandra has the below limitations:

- Distribution of the data is done only using the partition key which means if you choose wrong partition keys, it might result in hot spot issues where one node receives most of the load since each partition can be stored only in one node machine. Additionally, the maximum number of cells in each partition has an upper bound of 2 billion.

- Each column value can't be larger than 2GB.
- Collection values may not be larger than 64KB.

###### Data Modelling

Data modelling in Cassandra has the below main weaknesses:


- Not so easy to know all the possible query patterns of an application in advance.  Since the data model in Cassandra is based on the query patterns, you need to think of what are the possible queries that your database need to answer based on the application expected tasks. However, it might be difficult to think of and write down all the possible queries that the application might need at the early stages since it requires extensive knowledge about the application.

- If the query patterns changes in the future, then you need to change your data model and that might involve data migration or a lot of work.


- Choosing the partition key that will be used for evenly distributing the data across the cluster is a critical task and could be difficult sometimes. Although thinking of which columns to consider as clustering columns and which columns should be indexed might be also difficult in some scenarios depending on the queries.


##### Summary

Cassandra is suitable for enterprises that are having very large dataset and they are looking for a database to address problems related to performance, scalability and availability.  Cassandra is great when you have large workload that involves many writes but few reads such as storing and scaling millions of daily logs or sensor or IoT events . Cassandra has some limitations related to reading the data such as querying, searching, sorting or performing large scale aggregations or ad-hock queries. Therefore, Cassandra is not recommended when you need to run analytics or complex queries against your data or if your application involves many reads. Cassandra also expect an extensive knowledge of the current dataset since the data modelling is based on the query patterns. This means that if you are looking for an application transparency, then Cassandra isn't recommended for you. Additionally, Cassandra isn't recommended if you know that your data size is not large even in the future when your data grows. It is always possible to migrate to Cassandra when your data becomes so large and when you really need the features of Cassandra.  Finally, if you need a strong consistency most of the time, then Cassandra isn't of you since configuring Cassandra to be strong consistence will impact performance and reduce availability which what Cassandra is really good for. 





