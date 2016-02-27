Cassandra is a distributed database that can handle large amount of data and allows for high performance, scalability and availability because of its decentralised architecture. In the following sections, I will give a brief summary for most of the features that we look for in a database and how Cassandra supports them.


##### Use cases

Cassandra is currently used by many large companies such as Ebay, Apple and Netflix because of its scalability, high performance and availability. The main use case for Cassandra is to help scaling large time series data (data stored so frequently such as events). Storing and efficiently scaling this type of data is so useful in many use cases such as to build a recommendation system, storing IoT or sensor events, logging and messaging, fraud detection and many other use cases.

##### Basic Concepts

Main Concepts in Cassandra are:

Node: The basic infrastructure component where we store the data.

Data Center: A group of related nodes.

Cluster: The outermost container and can contain multiple data centres.

Gossip protocol: A protocol used by a node to discover information and the state of all other nodes in the cluster.

A Partitioner: Is a hash function used to compute the tokens (hashing the partition key) and this token is used to distribute the data as well as read/write request across all the nodes in the cluster based on the hash ranges (Consistent hashing). 

The replication factor: Used to determine how many copies of the written
data should be kept inside a cluster.

The Replication placement strategy: It is a strategy used by the node to determine where to replicate the data (in which nodes) when a write request received.

A Snitch: The snitch used to monitor all the nodes and provides performance and state information to help the replication strategy to decide where to replicate the data.

A Keyspace: A container that is used to group the different CQL tables of the application.

A CQL table: Collection of multiple ordered columns having a primary row key.

Primary key: Each CQL table should have a primary key which is used to ensure that the data is unique and willn't be overwritten inside each partition. The primary key contains the partition key and any clustering columns. The primary key can be a single key or a composite key (partition key is more than one column) or a compound key (partition key and some clustering columns).

Partition Key: This is the key used to divide your data into different partitions before distributing them across the nodes for scalability. The partition key can be either a single key or a composite key.

Clustering columns: Columns used to group and sort the data inside each partition.

Data Types: Many common data types support such as most numeric data types (int, float, double and decimal), text data types (text, varchar and even blob), collection data types (map, list and set), UUID and counter data types, boolean, timestamp and the support for custom defined data types which can contain any separate objects.

Compaction: A process that starts frequently to merge all the different versions of the data and create a final version having the latest written data. It is needed since the written data in Cassandra is immutable and can't be overwritten during data update or deletion.



##### Installing

Can be installed in most of the operating systems by building from the source using the  distribution binaries. In the linux-based systems, you can also install it using package managers such as apt-get and Yum. 


##### Query language

Cassandra supports a query language called CQL (Cassandra
Query Language) which has a SQL-similar syntax by supporting clauses such as CREATE TABLE, SELECT, WHERE, and ORDER BY.

##### Transaction support

Cassandra supports the ACID properties and trasnactions as explained below:

Consistency: Cassandra supports a configurable consistency levels or what is called a tunable consistency beside supporting a linearisable consistency that can be used in transactions.

Atomicity:  At the row level or the partition-level, the insert, update, and delete operations are all executed atomically depending on the consistency level configured.

Isolation: Cassandra provides full isolation at the low-level which means that any writes from a client application will not be visible to other until it is completed.

Durability: Cassandra is fully durable and all writes will be persisted on disk to a commit log first (for disaster recovery) then later to the on-disk SSTable files.


Transactions: Supports a transaction mechanism called lightweight transactions which are implemented using the Paxos protocol. Paxos is used to make all nodes agree on proposals based on the majority voting and ensures that there exists only one agreed values between
all the nodes.

##### Data Import and Export

CQL supports a statement called "COPY" to either export data from a table into a CSV file or import data from a CSV file and into a table. Additionally you can execute multiple CQL statement from a file as a batch using the SOURCE command.

##### Data Layout

Data modelling in Cassandra is relatively different if you are coming from a relational database background. The data in Cassandra is modelled around the query patterns instead of the data entities and relationships. Besides, it is absolutely ok to duplicate your data in different tables to achieve high performance during the retrieval of your frequent queries. 

##### Relational data

Since Cassandra doesn't support joins, relationships are modelled duplicating the data from the different related entities and group them into a separate CQL table that can be used to fulfil queries with the relationships. It is important to choose a correct primary key that contains proper partition key and any clustering columns if needed to be able to retrieve the related data efficiently later.

##### Normalisation/Denormalisation

It is usually encouraged to denormalise the data to achieve better performance and scalability.

##### Referential Integrity

There is no such concept as referential integrity, joins, or foreign keys in Cassandra.

##### Nested Data

Cassandra doesn't support nesting collections inside each other. However we can nest user defined data types inside collections.


##### Query

CQL supports a SELECT statement similar to SQL that can be used to run parametrised or range queries. The SELECT statement is usually used with the WHERE clause for filtering and can return either all fields or just some selected fields. Additionally, Cassandra supports querying some system tables to get information about available tables and columns, keyspaces, user-defined functions and data types or cluster related info. 


##### Aggregation

Cassandra has a built-in support for common aggregate functions such as min, max, avg, sum, and count. Aggregation is done in the partition-level in Cassandra, therefore choosing the appropriate partition key is necessary for a successful aggregation. Additionally, Cassandra  supports user defined functions that can be used to create a custom aggregate function based on the requirement.

##### Full Text Search

Cassandra doesn't support internally full-text search. However it supports creating a custom index besides there are some external plugins available for full-text search such as the Stratio’s Cassandra Lucene Index.

##### Regular Expressions

Cassandra has no built-in support for regular expressions and usually external search engines plugins are used for regular expression or full-text search support.


##### Indexing

Cassandra supports creating secondary indexes on any columns other than the primary key columns. The indexes are built in the background without blocking writes or reads and are maintained automatically by Cassandra. 


##### Filtering and Grouping

Cassandra supports the CQL WHERE clause to filter data. Unlike SQL, only columns that are part of the primary key or the secondary indexed columns can be filtered using the WHERE clause. You can use operators such as =, >, >=, <, and <= to filter the data. Additionally you can use the IN or CONTAINS clauses to filter the data based on a certain values in a collection.

Grouping the data in Cassandra is done using the partition key. The partition key can be simple (single column) or composite (multiple columns). Clustering columns are also used to group and sort the data inside each partition. Cassandra also supports collection data types such as list, map and set that can be used to group related data together. Additionally, Cassandra support the user defined data type which can represent a complete separate object and can be used to group related data as an object data type (e.g the customer address).


##### Sorting

Cassandra supports sorting using the clustering columns that should be defined during the table creation. The clustering columns can be later used to sort the data inside each partition in either ascending or descending orders using the CQL ORDER BY clause with the ASC or DESC options.

##### Configuration

Cassandra uses a yaml configuration file called cassandra.yaml to store all configuration related parameters. The configuration file can be found under the install_location/conf folder. Cassandra doesn't support changing the configurations on the fly and you will need to restart the node for the new configurations to take effect. 

##### Scalability

The client can read or write to any node in the cluster since Cassandra treats all nodes equally. This makes Cassandra highly scalable since scaling the system is as simple as adding or removing nodes or data centres. Additionally, the data is partitioned across all the nodes based on the partition key which allows even distribution of the data as well as the read/write throughputs. The client can read or write from any node and the request will be forwarded smoothly to the node having that portion of data.  Adding and removing the nodes to the cluster is easily done using virtual nodes paradigm.


##### Persistency

Cassandra is fully durable data store since the data will be written directly to a commit log once received. The commit log is then persisted to disk and later it can be used as a crash recovery mechanism whenever the node accidentally failed. The write operation will be considered a successful only once it has been written to the commit log. After the write operation is written to the commit log, it will be written to the MemTable where it will be kept until a pre-configured size threshold is reached. Once this threshold is reached, data will be flushed to disk or what is called the SSTable files and be persisted permanently.  

##### Backup

Cassandra supports taking backups through snapshots and by enabling the incremental backup feature. The snapshot is taken for the SSTable files in a node either for a single Keyspace or for all Keyspaces or even for the whole cluster. Generally, a system-wide snapshot is taken then the incremental backup feature is enabled in each node to backup only the delta data that has been changed since the last snapshot. 

##### Security

Cassandra provides security by providing features such as the ability to use SSL encryption for all the communications between the client and the nodes. Also Cassandra supports authentication based control by creating users and roles using SQL-similar statements such as CREATE USER and CREATE ROLE. Additionally, Cassandra allows granting or revoking permissions from users on specific objects using CQL GRANT or REVOKE statements.

##### Upgrading

Upgrading Cassandra is relatively easy which needs to be done for each node in the cluster  and it will require stoping, reconfiguring and restarting the node. 

##### Availability

Cassandra is a highly available distributed system since it replicate the data across multiple nodes or even data centres that spans multiple locations. Additionally, Cassandra is a decentralised system that doesn't have a single point of failure since it is based on a distributed design where all nodes are equally important and any node can receive read/write requests from the client application. When creating a Keyspace, the replication factor and replication strategy needs to be configured. The replication factor will determine how many copies we need to keep for each written data. The replication strategy will decide where (in which node) to replicate the data based on information provided by a snitch that continuously monitor the nodes performance and status. The replication strategy needs to be selected carefully since it will directly impact the availability.