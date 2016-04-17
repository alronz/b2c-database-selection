

**Factors Influencing the Adoption of a NoSQL Solution : A focus on the data modelling and query capabilities.** 



 *An attempt to analyze different NoSQL databases from different NoSQL categories by studying their data modelling and query capabilities. In the process, many features of each database are investigated and documented in an easy tutorial-like approach. Based on the results of the analysis, the strengths and weaknesses of each database as well as the factors that influence the adoption of a NoSQL solution are presented* 


---

## Introduction

For a long time, relational databases have been the default choice for serious data storage especially for enterprise applications. Currently, many other database technologies are grabbing more attention due to their high performance, scalability and availability options such as Not Only SQL (NoSQL) technologies. Choosing the right database technology for your application among a plethora of database options is a challenging task.
In this website, I aim to provide a systematic and experimental evaluation for four NoSQL databases across spectrum of different NoSQL categories. In the process, I captured various of functionalities and trade-offs each database has. Multiple aspects have been examined such as the database transaction support, query options, data layout, scalability, availability, security, and durability options. The data modelling of each database is also analyzed using a data model from the Transaction Processing Council Ad-hoc (TPC-H) benchmark [1].Furthermore the query capabilities of each database were evaluated using three complex queries from the same benchmark. At the end, I will present a set of factors that influence the adoption of a NoSQL solution. I hope that these factors can assist users and organisations to choose the right database technology for their specific application needs.


## NoSQL Database Selection

There are multiple approaches to classify NoSQL databases. A basic classification is based on the database data model as explained below:

 - Document-based databases : store the data as documents. Example are MongoDB, and CouchDB.
  - Key-value databases : store the data in dictionary-like data structures where data is accessed by a key. Examples are Redis, Dynamo, and MemcacheDB.
  - Column-based databases : store the data in tables but grouped as columns instead of rows. Examples are Cassandra, and HBase.
  - Graph-based databases : store the data in a graph structure. Examples are Neo4j, and OrientDB.A database from each category is chosen based on many criteria such as the database maturity, popularity, availability, performance, reliability, scalability, community strength, and open source support. 
The database popularity is determined based on the results of the DB-Engines Ranking which ranks databases based on different metrics such as Google trends, frequency of technical discussions, number of job offers, and relevance in social networks [2]. 
Another metric used is the Magic Quadrant for Operational DBMS 2015 published by Gartner Inc. which consists of series of market research that provide qualitative analysis of the operation DBMS market and its direction [3].
Based on the mentioned criteria, the below NoSQL databases have been selected :
 - **Redis**: Redis is an extremely fast NoSQL key-value in-memory database which stores data in different useful data structures such as strings, list, sets and sorted sets. Redis supports many powerful features like built-in persistency, pub/sub, transaction support with optimistic locking and Lua Scripting. It is ranked as the most popular key-value database as per the DB-Engines Ranking as per September 2015. Furthemore, it is in the leaders quadrant on the Magic Quadrant for Operational DBMS 2015.
 - **MongoDB**: MongoDB is an open source document-based database that stores the data as documents and provides more flexible data modeling. MongoDB allows for better performance, availability and scalability by using features such as sharding, replication, embedded documents, indexing, aggregation pipeline and automatic fail-over. MongoDB is the most popular database based on the DB-Engines Rankingas as per September 2015. Furthemore, it is in the leaders quadrant on the Magic Quadrant for Operational DBMS 2015.
- **Cassandra**: Cassandra is a wide-column distributed database that can handle large amount of data and allows for high performance, scalability and availability because of its decentralised architecture. Cassandra is also the most popular column-based database based on the DB-Engines Ranking as per September 2015. Furthemore, it is in the leaders quadrant on the Magic Quadrant for Operational DBMS 2015.
- **Neo4j**: Neo4j is an open-source graph-based database that was built completely based on the graph property model. Neo4j is compliance with the database ACID properties and supports non-functional requirements such as limited scalability, availability and fault-tolerance. It is also the most popular graph database based on DB-Engines Ranking  as per September 2015. Although it comes in the niche players quadrant on the Magic Quadrant for Operational DBMS 2015, however it is one of the few graph databases that made it to the Gartner Magic Quadrants.

## Data Modelling Testing

The data model used to test the data modelling capabilities of each database is taken from the TPC-H benchmark as seen below. It consists of eight individual tables and some relationships between them. It is a relational database model since this benchmark is intended to evaluate the performance of relational databases. This data model is chosen because it is somehow complete and can reflect a model of a real B2C application.  Using a complete real data model can be useful to explore the data modelling capabilities of the investigated database.


![image](https://s3.amazonaws.com/b2cbucket/tpch_schema.png)


## Query Capabilities Testing

The below three SQL queries are chosen from the TPC-H benchmark to test the query capabilities for each of the investigated databases.

 - **Pricing Summary Report Query (Q1)**: This SQL query is used to report the amount of billed, shipped and returned items. This query was selected to explore how the advance aggregation, sorting and filtering is supported in the selected databases. The query is shown 
below :

```sql
select
   l_returnflag,
   l_linestatus,
   sum(l_quantity) as sum_qty,
   sum(l_extendedprice) as sum_base_price,
   sum(l_extendedprice*(1-l_discount)) as sum_disc_price,
   sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge,
   avg(l_quantity) as avg_qty,
   avg(l_extendedprice) as avg_price,
   avg(l_discount) as avg_disc,
   count(*) as count_order
from
   lineitem
where
   l_shipdate <= date '1998-12-01' - interval '[DELTA]' day (3)
group by
   l_returnflag,
   l_linestatus
order by
   l_returnflag,
   l_linestatus;
``` 
 
 
  
 - **Shipping Priority Query (Q3)**: This query is used to get the 10 unshipped orders with the highest value. In order to do that, the query joins different three tables (customer, order and lineitem). This query is selected to investigate how querying related data is supported in the investigated databases. The query is shown below:
 
```sql
select
 l_orderkey,
 sum(l_extendedprice*(1-l_discount)) as revenue,
 o_orderdate,
 o_shippriority
from
 customer,
 orders,
 lineitem
where
 c_mktsegment = '[SEGMENT]'
 and c_custkey = o_custkey
 and l_orderkey = o_orderkey
 and o_orderdate < date '[DATE]'
 and l_shipdate > date '[DATE]'
group by
 l_orderkey,
 o_orderdate,
 o_shippriority
order by
 revenue desc,
 o_orderdate;
``` 
 
 - **Order Priority Checking Query (Q4)**: This query is used to see how well the order priority system is working and gives indication of the customer satisfaction. This query was selected to check how sub-queries are supported in the investigated databases. The query is shown below:
 
```sql
select
 o_orderpriority,
 count(*) as order_count
from
 orders
where
 o_orderdate >= date '[DATE]'
 and o_orderdate < date '[DATE]' + interval '3' month
 and exists (
select
 *
from
 lineitem
where
 l_orderkey = o_orderkey
 and l_commitdate < l_receiptdate
)
group by
 o_orderpriority
order by
 o_orderpriority;
``` 
    


## The Results of the Analysis

The results of the anlaysis is documented for each database. Below the contents of this documentation is listed. To read about how the testing of the data modelling and query capabilities of each database is performed, please check the "Examples" section for each database. The code is also available in the [github repository](https://github.com/alronz/NoSQL-Selection-Factors). Furthermore, the strengths and weaknesses of each database are found under the "Results" section for each database. A short summary for all the analyzed features of each database is also found under the same section.


   - Key-Value
      - [Redis](Redis/Redis.md)
        - [Basics](Redis/Basics/Basics.md)
        - [Data Model](Redis/Data Model/Data Model.md)
        - [Query Model](Redis/Query Model/Query Model.md)
        - [Administration and Maintenance](Redis/Administration and Maintenance/Administration and Maintenance.md)
        - [Special Features](Redis/Special Features/Special Features.md)
        - [Examples](Redis/Examples/Examples.md)
        - [Results](Redis/Results/Results.md)
   - Document-Based
      - [MongoDB](MongoDB/MongoDB.md)
        - [Basics](MongoDB/Basics/Basics.md)
        - [Data Model](MongoDB/Data Model/Data Model.md)
        - [Query Model](MongoDB/Query Model/Query Model.md)
        - [Administration and Maintenance](MongoDB/Administration and Maintenance/Administration and Maintenance.md)
        - [Special Features](MongoDB/Special Features/Special Features.md)
        - [Examples](MongoDB/Examples/Examples.md)
        - [Results](MongoDB/Results/Results.md)
   - Graph-Based
      - [Neo4j](Neo4j/Neo4j.md)
        - [Basics](Neo4j/Basics/Basics.md)
        - [Data Model](Neo4j/Data Model/Data Model.md)
        - [Query Model](Neo4j/Query Model/Query Model.md)
        - [Administration and Maintenance](Neo4j/Administration and Maintenance/Administration and Maintenance.md)
        - [Examples](Neo4j/Examples/Examples.md)
        - [Results](Neo4j/Results/Results.md)
   - Wide-Column
      - [Cassandra](Cassandra/Cassandra.md)
        - [Basics](Cassandra/Basics/Basics.md)
        - [Data Model](Cassandra/Data Model/Data Model.md)
        - [Query Model](Cassandra/Query Model/Query Model.md)
        - [Administration and Maintenance](Cassandra/Administration and Maintenance/Administration and Maintenance.md)
        - [Examples](Cassandra/Examples/Examples.md)
        - [Results](Cassandra/Results/Results.md)


## Evaluation

In the following sections, I give a comparison summary for all the main analysed features for all the four investigated databases. For more details, please have a look at the [previous section](#the-results-of-the-analysis) where each feature for each database is explained in more detail. Furthermore, I explain the main strengths and weaknesses of the investigated databases as well as the main factors that influence the selection of a NoSQL database.


#### Basics Comparison

In the table below, I provide a comparison summary for some of the main basic features of the investigated databases.


|                        | Redis                                                                                                                                                 | MongoDB                                                                                                                 | Cassandra                                                                                                                                                   | Neo4j                                                                                              |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| **Underlying Structure**    | - Data structures such as strings,  lists, hashes, sets and sorted sets.<br> - Strings can be also integers, floating point values or even a binary data. <br> | - Data stored as documents in a json-like  format called BSON.<br>  - Collections are used to group documents. <br>             | - Stored data as ordered columns  having a primary row key.<br> - Columns stored inside a CQL tables.<br> - CQL tables stored inside a keyspace.<br> - Many data types.<br> | - Stored data as nodes and relationships.<br> - Nodes can have labels.<br> - Relationships can have types.<br> |
| **Use Cases**              | - caching.<br> - message broker.<br> - chat server.<br> - session management.<br> - queues.<br> - real time analytics.<br>                                                    | - event logging.<br> - content management.<br> - blogging platforms.<br>                                                            | - scaling large time series data.<br> - storing IoT or sensor events.<br> - logging and messaging.<br>                                                                   | - path finding problems.<br> - recommendation system.<br> - social networks.<br> - network management.<br>         |
| **Query Language**         | - set of commands.<br>                                                                                                                                    | - CRUD operations at collection level. <br>                                                                                 | - SQL-like query language called CQL.<br>                                                                                                                       | - declarative query language called Cypher.<br>                                                        |
| **Transaction Support**    | - yes, using MULTI/EXEC, WATCH, and UNWATCH commands.<br>                                                                                                 | - transaction possible in a single document.<br> - no internal transaction support across documents. <br>                         | - lightweight transactions using Paxos protocol. <br>                                                                                                           | - yes, all write operations run on a transaction. <br>                                                 |
| **Data Import and Export** | - mass insertion using "redis-cli --pipe".<br>                                                                                                             | - db.collection.bulkWrite() to execute a  batch of write operations.<br> - mongoexport and mongoimport tools are supported.<br> | - COPY commands is used to export or import data in CSV format. <br>                                                                                               | - LOAD CSV Cypher command to import/export data.<br>                                                   |

#### Data Model Comparison

In the table below, I provide a comparison summary for some of the main features related to the data model of the investigated databases.


|                         | Redis                                                                                               | MongoDB                                                                                                                                                                                                             | Cassandra                                                                                 | Neo4j                                                                                                                                                                                                             |
|-------------------------|-----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Data Layout**             | - No schema design upfront.<br> - Choose data types to represent the data.<br>                              | - Schema-less.<br> - Doesn't enforce documents structure.<br> - Data modelling is more flexible.<br> - Normalized model by using references.<br> - Denormalized model by embedding documents.<br>                                       | - Data model around the query patterns.<br> - Data redundancy is acceptable.<br>                  | - Convert data into nodes and relationships to build graph model.<br> - Identify node labels and relationship types.<br> - Data stored as node properties.<br> - Relationship related data stored as relationship properties.<br> |
| **Relational Data Support** | - Modelling complex data isn't recommended.<br> - Relations can be modelled using sets and sorted sets.<br> | - Denormalized approach for one-to-one relationship.<br> - Normalized/Denormalized approaches for one-to-many relationship depending on the document size growth.<br> - Normalized approach for many-to-many relationship.<br> | - no joins.<br> - relationships are modelled by creating a table for each relationship query.<br> | - all relationships are represented in the same way through one relationship.<br>                                                                                                                                     |


#### Query Model Comparison

In the table below, I provide a comparison summary for some of the main features related to the query model of the investigated databases.


|                     | Redis                                                                                           | MongoDB                                                                                                                                                                                                                     | Cassandra                                                                                                                                                           | Neo4j                                                                                          |
|---------------------|-------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| **Query**               | - Parameterised queries using the key.<br> - Range queries using sorted Sets.<br>                       | - The find() command with selection and projection statements.<br> - Parameterized and range queries support with many query selectors.<br> - Query array content using $elemMatch.<br> - Query embedded documents using dot notations.<br> | - Using CQL SELECT statement.<br> - Parameterized/Range queries require indexes.<br>                                                                                        | - Using the Cypher MATCH clause.<br> - Write complex queries easily.<br> - Efficient graph traversing.<br> |
| **Text Search Support** | - No full-text support.<br> - some commands uses regular expressions.<br>                               | - Full-text search supported using the Text Index.<br> - Regex supported using the $regex keyword.<br>                                                                                                                              | - No full-text search support.<br> - No regex support.<br>                                                                                                                  | - No full-text search support.<br> - Java regex support.<br>                                           |
| **Aggregation**         | - No aggregation support.<br>                                                                       | - Aggregation supported using the aggregation pipeline framework or a map reduce.<br>                                                                                                                                           | - Aggregation functions supported.<br> - Aggregation at the partition level.<br>                                                                                            | - Aggregation supported.<br> - Many aggregation functions.<br>                                         |
| **Indexing**           |  - The key is the primary index.<br> - No secondary indexes support.<br>                                | - Many index types are supported.<br>  - Defined on the collection level.<br>                                                                                                                                                       | - Secondary index supported.<br>                                                                                                                                        | - Secondary index support.<br>                                                                     |
| **Sorting**             | - A Sort command is supported.<br>                                                                  | - A sort method is supported.<br>                                                                                                                                                                                               | - Sorting supported using the clustering columns.<br> - clustering columns defined during the table creation.<br> - Using CQL ORDER BY clause with the ASC or DESC options.<br> | - ORDER BY clause to sort the results.<br>                                                         |
| **Filtering**           | - Sets can be used to filter data using set commands such as intersect, union, and difference.<br>  | - Filtering using a query statement within the Find() method.<br>                                                                                                                                                               | - CQL WHERE clause to filter data.<br> - Only primary key, indexed columns can be filtered.<br>                                                                             | - Using the MATCH and WHERE clauses.<br> - Based on a match pattern.<br>                               |


#### Quality Attributes Comparison

In the table below, I provide a comparison summary for all the main features related to some of the quality attributes of the investigated databases.


|  	| Redis 	| MongoDB 	| Cassandra 	| Neo4j 	|
|--------------	|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|------------------------------------------------------------------------------------------------------	|---------------------------------------------------------------------------------------------------------------------------------------------------------	|------------------------------------------------------------------------------------------------------------------------	|
| **Scalability** 	| - Scaling reads using replications.<br>  - Scaling writes using sharding.<br>  - Auto-sharding and balancing using Redis cluster.<br>  - Scaling configuration using Redis Sentinel. <br>	| - Scaling using the Sharded Cluster. <br> - Auto-sharding and balancing.<br>  	| - All nodes accept read/write requests.<br>  - Scaling by adding and removing nodes.<br>  - Auto-partitioning based on partition key. <br>  	| - Scale reads using the HA master-slave clusters.<br>  - Only vertical scaling the write load.<br>  - No support for sharding.<br> 	|
| **Persistency** 	| - Using Snapshoting and AOF.<br>  - The fsync options determine the durability levels. <br>	| - Two persistent storage engines.<br>   - Journalling for more durable solution in the event of failure. <br>	| - Fully durable. <br> - Data is written directly to a commit log. <br> 	| - Fully durable.<br>  - Once transactions is committed, it will be written directly to disk.<br> 	|
| **Security** 	| - No access control.<br>  - BIND command to allow specific IP to access the server.<br>  - Supports authentication mechanism. <br> - No support for encryption mechanism.  <br> - "rename-command” used to rename the original commands. <br>	| - Authentication and role-based access control. <br>  - Communication encryption using TLS/SSL protocols <br>	| - SSL encryption. <br> - Authentication based control.<br>  - SQL-similar statements.<br>  - Granting or revoking permissions from users on specific objects. <br>	| - Basic security support. <br> - Authentication mechanism. <br>	|
| **Availability** 	| - Redis Sentinel to guarantee high availability. <br> - Automatic failover. <br>	| - Replication using the Replica Set. <br> - Automatic failover supported. <br>	| - Highly available distributed system. <br> - Replication support. <br> - Decentralised system, no single point of failure. <br> - All nodes are equally important. <br>	| - Using the master-slave HA cluster.<br>  - Replication support.<br>  - Automatic failover.<br> 	|

#### Strengths & Weaknesses Comparison

In this section, I give a brief summary for the main strengths and weaknesses for each of the investigated databases.


|           | Strengths                                                                                                                                                                 | Weaknesses                                                                                                                                                                                | Suitable for Applications                                                                                | Not Suitable for Applications                                                                     |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Redis**     | - More complex data type support.<br>  - High availability solution support.<br>   - On-Disk persistence.<br>  - Scalability options.<br>  - Transaction support.<br>   - Better performance.<br> | - Difficult to model relational data.<br>   - Dataset is limited by the memory size.<br>   - Query and aggregation limitations.<br>   - No support for secondary indexes.<br>   - Basic security options.<br> | - With foreseeable dataset size.<br>  -  With high performance requirement.<br>                                  | - With deeply connected data.<br>  - With rich queries requirements.<br>  - With large dataset (costly).<br>  |
| **MongoDB**   | - Rich query capabilities.<br>   - Flexible document data model.<br>   - Auto-sharding and balancing.<br>   - Replication and auto failover.<br>                                          | - Data should fit the document data model.<br>   - Aggregation and queries are executed at the collection level.<br>  - No transaction support.<br>                                                   |   - With data that fits the document data model.<br>                                                         | - With deeply connected data.<br>  - With transaction requirements.<br>                                   |
| **Cassandra** | - Elastic scalability.<br>  - The Distributed architecture.<br>   - Fault tolerance, availability support.<br>  - Simple query language.<br>  - High performance.<br>                         | - Query, sorting and aggregation limitations.<br>  - Data modelling challenges.<br>                                                                                                               |  - With large dataset.<br>  -  With high performance, scalability, and availability requirements.<br>            |  - With rich query requirements.<br>  - With small dataset.<br>  - With strong consistency requirements.<br>  |
| **Neo4j**     | - Powerful query model.<br>   - The graph model flexibility.<br>   - Easy to learn declarative query language.<br>   - ACID compliant and full transaction support.<br>                   | - Scalability limitations.<br>  - Date support limitation.<br>                                                                                                                                    |  - With deeply connected data.<br>  - With complex queries requirements.<br>  - With data having a graph nature.<br> |  - With simple data model.<br>  - With elastic write scalability requirements.<br>                        |


#### Factors Influencing the Adoption of a NoSQL Solution 

Based on the analysis of the four NoSQL databases, the following factors have been found that affect the decision of adopting a NoSQL solution:

##### Data Related Factors

NoSQL databases are designed to support a variety of data models. Therefore, it is imperative to first analysis the application’s data nature before migrating to a NoSQL solution. Some data features that should be investigated are the schema flexibility, data size, data complexity, relational data support and data access patterns. For instance, the data complexity and relational data support of the investigated databases are shown below. For example, if the data is deeply connected, then Neo4j might be the right choice. For more information about the data modeling capabilities for each of the investigated databases, see section [Data Model Comparison](#data-model-comparison).

![image](https://s3.amazonaws.com/b2cbucket/queryVsDataModel.png)

##### Query Related Factors

These factors are related to the query capabilities required for the application. Basedon the query requirements, it would be possible to choose a NoSQL solution thatmeets these requirements. Examples of the requirements are if the application requiresfull-text search, regular expressions, indexes, aggregation, filtering, range queries,Ad-Hoc queries, and joins support. For more details about the query options for eachdatabase, see [Query Model Comparison](query-model-comparison).
##### Non-Functional Related Factors
Most of the factors influencing the adoption of a NoSQL database are related to thenon-functional requirements of the application. Below are the main non-functionalproperties of a database system:
- **Performance**: The performance is one of the most important concerns to adopt a NoSQL solution, unfortunately, it wasn't analysed here.
- **Scalability**: This includes concerns such as whether the database supports horizontalscaling, auto-sharding and load balancing. A summary of the scalability options for the selected databases is explained in [Quality Attributes Comparison](#quality-attributes-comparison).
- **Availability**: Check whether the database guarantees the availability requirement using techniques such as replication, auto-failover, and backups. A summary of the availability options for the selected databases is illustrated in [Quality Attributes Comparison](#quality-attributes-comparison).
- **Durability**: Check what are the durability options available for the database. A summary of the durability options for the selected databases is described in [Quality Attributes Comparison](#quality-attributes-comparison).
- **Transaction Support**: If the application requires to run ACID transactions or not. A summary for the transaction support for the selected databases is definedin [Basics Comparison](#basics-comparison).
- **Security**: Which security measures are required for the application. This includes security techniques such as communications encryption, authentication, and access control mechanisms. A summary for the security option for the selected databases is demonstrated in [Quality Attributes Comparison](#quality-attributes-comparison).
- **Database overall simplicity**: This includes concerns related to how easy it is to adapt and learn the new database solution. Examples are the simplicity of installation, upgrade, configuration, backup, and drivers support. Most of these concerns are summarized in [Quality Attributes Comparison](#quality-attributes-comparison), and [Basics Comparison](#basics-comparison).
- **Other factors**: Examples are the database licence type, community and documentation strength, database maturity, and popularity. These factors are important but are out of the scope of this research.



## References

[1]: TPC. TPC-H is an ad-hoc, decision support benchmark. url: http://www.tpc.org/ tpch/ (visited on 03/2016).

[2]: DB-Engines. DB-Engines Ranking. url: http://db-engines.com/en/ranking (visited on 03/2016).

[3]: Gartner. InterSystems Recognized as a Leader in Gartner Magic Quadrant for Operational DBMS. url: http://www.intersystems.com/our-products/cache/intersystems-recognized-leader-gartner-magic-quadrant-operational-dbms (visited on 03/2016).

[4]: RedisLabs. Redis. url: http://redis.io/documentation (visited on 03/2016).

[5]: J. L. Carlson. Redis in Action. Manning, 2013.

[6]: K. Banker. MongoDB in Action. Manning, 2012.

[7]: R. Bradberry and E. Lubow. Practical Cassandra: A Developer’s Approach. Addison-Wesley, 2013.
[8]: EndPoint. “Benchmarking Top NoSQL Databases.” In: (2015). doi: http://www.datastax.com/wp-content/themes/datastax-2014-08/files/NoSQL_Benchmarks_EndPoint.pdf.
[9]: T. A. Foundation.Welcome to apache cassandra. url: http://cassandra.apache.org/ (visited on 03/2016).[10]: M. Inc. Mongodb 3.2 manual. url: http://docs.mongodb.org/manual (visitedon 03/2016).[11]: Neo4j. Graph DB vs Rdbms. url: http://neo4j.com/developer/graph-db-vsrdbms/(visited on 03/2016).[12]: N. Technology. Neo4j Documentation. url: http://neo4j.com/docs/ (visited on03/2016).


