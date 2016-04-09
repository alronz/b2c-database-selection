
[Home](../../index.md)

[Neo4j Content](../Neo4j.md)
___

# Neo4j > Results > Summary


Neo4j is an open-source graph based database that was built completely based on the graph property model. Neo4j is compliance with the database ACID properties and supports non-functional requirements such as scalability, availability and fault-tolerance. In the following sections, I will give a brief summary for most of the features that we look for in a database and how Neo4j supports them.


##### Use cases

Neo4j is based on the property graph model and can handle complex queries very efficiently. Therefore, it is used in many use cases such as software analytics, scientific research, path finding problems, recommendation system, social networks, network management, business intelligence or warehouses and many more. 

##### Basic Concepts

Main concepts used in Neo4j.

Nodes: The fundamental unit used in Neo4j to represent objects or entities that needs to be stored in the database.

Relationships: Represents the relationships between two nodes.

Properties: Nodes and relationships can use one or more properties to store some information related to the node or the relationship.

Labels: are markers used to group a set of similar nodes.

Relationship Types:  are markers used to group a set of similar relationships.

Traversal: How the database search the graph to find results of the queries


Paths: The traversal result. It contains one or more nodes connected with some relationships


##### Installing

Neo4j can be installed easily in most of the platforms. Neo4j supports a windows installer for windows systems and a DMG installer for mac systems. Neo4j can be installed using the apt-get package manager in linux-based systems.


##### Query language

Neo4j uses a declarative query language called Cypher which is a user friendly language inspired by SQL and has a pattern matching expressions similar to SPARQL.  The Cypher language is used to create nodes and relationships and to run simple or complex queries. 


##### Transaction support

Neo4j is compliance with the ACID properties and provides full transaction support. All operations in Neo4j that changes the graph will be run on a transaction. You can also  execute multiple update statement in one transaction. The transaction can be committed or rolled back. Usually a try-catch block is used where we commit at the end of the try block or rollback inside the catch block. Transactions in Neo4j are having read-committed isolation level which means that we can read only the committed changes. Default write locks are used in Neo4j whenever we create,update or delete a nodes or a relationships. The locks are added to the transaction and released whenever the transaction completes. Additionally, explicit write locks for nodes and relationships can also be enabled to provide higher serialisation isolation level. Neo4j is also fully durable and whenever the transaction is committed, the data will be persisted on disk.


##### Data Import and Export

Neo4j supports loading data as parameters that can be either scalar values, maps, lists or lists of maps. The UNWIND clause can be used to expand and insert the imported data one by one to create the graph. Additionally, The data can be imported as JSON data pulled from some API or from CSV files using the LOAD CSV Cypher command. Neo4j supports also exporting the data easily in JSON format. 


##### Data Layout


Modelling the data in Neo4j requires converting the data to nodes and relationships to build the graph model. For example to convert the data from the relational model to the graph model, we first create node labels that corresponds to table names. Then each row in the relational data model will correspond to a node in the graph model and will be tagged with the appropriate label. The columns in the relational tables can be stored as properties inside each node ignoring any columns having null values. Finally, primary keys are enforced using unique constraints and foreign keys will be replaced with relationships. 


##### Relational data

In Neo4j all relationships regardless of whether they are one-to-one, one-to-many or many-to-many are represented in the same way through one relationship. Although relationships in Neo4j are directed but they can be traversed in both direction which means that you just need to create one relationship between different nodes regardless of the direction. It makes sense to create more than one relationship between two different nodes if they are of a different type or meaning since later you can use these different relationships to answer different queries.


##### Normalisation/Denormalisation

Using the graph data model, you can model your data as normalised as you want without any problems. Actually using the graph data model, you can keep your data as it is in the relational model: small, fully normalised and yet represent richly connected entities.

##### Referential Integrity


In Neo4j, if we delete a node, all its relationships will be also removed since we can't have a hanging relationship without a start or end node. Data integrity in Neo4j ensures that there shouldn't be any relationship without start or end node and there shouldn't be any properties that aren't attached to a node or a relationship. 


##### Nested Data

In Neo4j, the concept of nested data isn't applicable since storing nodes inside other nodes isn't support and it makes more sense to store related data as separate nodes having separate properties and then connect them using relationships. 


##### Query

Neo4j supports parameterized or range queries by using the MATCH and WHERE clauses. Querying the graph model is based on the matching patterns provided in the MATCH part. This could mean searching the whole graph or just a subgraph based on the node labels and relationship types. Neo4j Cypher language allows for writing simple or complex queries easily and traversing the graph to search for the results is usually so efficient.


##### Aggregation

Aggregation in Neo4j is easily supported using Cypher language by using many functions such as min, max, avg, sum, count, stdev, stdevp and collect. The aggregation is done in the RETURN clause and the grouping key(s) will be automatically figured out by the RETURN clause and doesn't need to be specified manually. 


##### Full Text Search

Neo4j doesn't provide any internal full-text indexes that can be used to implement a full-text search. However the use of a external index engine such as Apache Lucene full-text index engine is supported.


##### Regular Expressions


Neo4j supports filtering results using java regular expressions which includes some flags to specify how strings are matched such as "?m" for multiline, "?i" for case-insensitive, and "?s" for dotall.



##### Indexing

Neo4j supports the creation of indexes on any property of a node if the node is already tagged to a label. Indexes will be managed automatically by Neo4j and be kept always up to date whenever graph data is updated. Other types of indexes such as full-text, spatial, and range indexes are still not addressed but are planned to be targeted in the future roadmap for Neo4j.  


##### Filtering and Grouping

Filtering: Filtering the data in Noe4j is done by providing a specific match pattern that would be the input for a Cypher MATCH clause and then filter the returned results later using the WHERE clause. The pattern can handle very complex queries and should describe what we are looking for in a human readable style. We can also do the matching in multiple stages by either using more than one MATCH clause or by combining results from different MATCH clause using the WITH clause. The results of the MATCH clause can be then filtered out easily using the WHERE clause.

Grouping: Grouping in Neo4j is handled by using node labels and relationship types. A node can have one or more labels which can be used later to query only a certain group of nodes in the graph which results in faster queries. Relationships also can be grouped with relationship types. 


##### Sorting

Neo4j supports SQL-like clause called ORDER BY which is used at the end of your query after the RETURN or WITH clauses to sort the results by one of the returned properties. You can only sort by a property that has been projected by the RETURN or WITH clauses. Additionally, you can specify the sort order by using either DESC or ASC.


##### Configuration

A Neo4j database server instance can start without configuration since it uses the default configurations. The configurations are stored inside the configuration file conf/neo4j-server.properties. Inside this file, many configurations can be changed such as configuring security, performance, and many other operating features.

##### Scalability

Neo4j supports HA master-slave clusters that can be used to linearly scale reads where slaves can share the read load.  As for the write load, only the master instance in the cluster can handle it. Other slave instances can still receive the write requests from clients but then these requests will be forwarded to the master node. This means that Neo4j doesn't scale the write load very well and in case of exceptionally high write loads, only vertical scaling of the master instance is possible. Although it is possible to build some sharding logic in the client application to distribute the data across number of servers, however the sharding logic still not natively supported by Neo4j. This is because sharding the graph is a near impossible or NP hard problem. In general, sharding the data in the client side depends on the graph structure. If the graph has clear boundaries, then it can be sharded easily otherwise it can be really difficult. The future goal of Neo4j is to support sharding across multiple machines without the application-level intervention, however this might take time. Finally since each server in the cluster holds the full dataset, there is an upper bound limit for the graph size that can be stored in each server. For the current version, Neo4j supports up to around 34 billions of nodes and relationships and around 274 billions of properties. This is quite enough for large graphs of Facebook similar network graph size.


##### Persistency

Neo4j is fully durable and all written data is persisted on disk. Once transactions are marked as committed, it will be written directly to disk. 

##### Backup

Neo4j support an online backup tool called neo4j-backup that will be enabled by default and will take a local copy of the database automatically. First it will take a full backup then it will take an incremental backup. Restoring the backup is as easy as replacing the database folder with the backed up folder.

##### Security

Neo4j supports different security options on the data level as well as on the server level. In the data level, you can use the security methods available in the Java programming language such as encrypting the data. In the server level, the server replies only to request that are coming from the pre-configured adress and port. Additionally, clients can supply authentication credentials when trying to call the REST API and SSL secured communication over HTTPS is supported.


##### Upgrading

Neo4j supports automatic upgrade for upgrades between patch releases and within the same minor version. However, upgrading a major release is manual.

##### Availability


High availability features are supported using the HA cluster which is based on a master-slave configuration architecture. More than one slave can be configured to be the exact replica of a master and can be used to make the system continue working in case of hardware failure. The slaves can be also used to handle some of the read load and can accept write requests from the clients. However the slaves will synchronise the write with the master to maintain consistency. Once the master receives a write request from the slave, it will propagate the change to all other slaves. The instances in a HA cluster will monitor each other for availability and monitor if any new instance has joined or any existing instance has left the cluster. Automatic election will take place if the master left the cluster for any reason. During master failover, a new master will be automatically elected and during this time the writes will be blocked until the new master is available. It is also possible to use arbiter instances which are instances that have only one role which is to participate in election in case there is no odd number of nodes and an instance is needed to break the tie. 
