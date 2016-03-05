
MongoDB is an open source database that uses the document model which stores the data as documents and collections to provide more flexibility in terms of data modeling. MongoDB provides high performance, availability and scalability by using features such as sharding, replication, embedded documents, indexing, aggregation pipeline and automatic failover.


##### Use cases

MongoDB is based on a flexible document model and uses the rich BSON format representation. Additionally it provides high performance, horizontal scalability, high availability and fault tolerance. This makes it suitable for many use cases such as event logging, content management, blogging platforms, real time analytics, or web and ecommerce applications. 

##### Basic Concepts

The main concepts used in MongoDB:

Documents: MongoDB stores the data as documents which contains key-value pairs and have a structure similar to a map. They grow in size up to 16MB and each document should have an _id field which will be indexed automatically. The _id field is generated automatically if not specified and is used as a unique identifier for the document. The data inside the document is stored in a json-like format called BSON.


Collections: Are used to group multiple documents and are analog to tables in RDBMS. Collections can contain documents with different schema, however it is always encouraged that each collection stores documents with similar schema.


BSON: MongoDB uses a BSON format to represent its documents. BSON is a binary encoded format that extends the well-known JSON model to provide more data types support. BSON also is a lightweight efficient format. It is also very fast and traversable.


##### Installing

Can be installed in most of the operating systems by building from the source using the  distribution binaries. In the linux-based systems, you can also install it using package managers such as Yum. In Windows system, you can use the installation file (a file with .msi extension) to install it by following simple installation wizard. In Mac OS, it is possible to install it using Homebrew package manager.


##### Query language

MongoDB provides normal CRUD operations to interact easily with your stored data. These operations can run only at the collection level. To query the data, the db.products.find() can be used. To insert the data, many commands can be used such as db.collection.insertOne(), db.collection.insertMany(), and db.collection.insert(). To update the data, multiple commands are also supported such as db.collection.updateOne(), db.collection.updateMany(), db.collection.update(). Finally, to remove the data, commands such as db.collection.deleteOne(), db.collection.deleteMany() and db.collection.remove() can be used.

##### Transaction support

On the level of a single document, MongoDB guarantees that the write operation is atomic. This means that if you try to update, insert or delete a single document in MongoDB, the operation will be applied in the form of "all or nothing". However if you try to do a write operations for multiple documents, the operations will be applied atomically for each single document alone but the operations as whole may interleave and are not atomic. Hence the transaction is possible using MongoDB if you model your data in a single document and not supported internally if you plan to do it across multiple documents.  There are workaround methods that can be used to support transactions across multiple documents such as the Two Phases Commits pattern. Isolation can be supported across multiple documents using the $isolated option. Finally, concurrency control (allowing multiple applications to access documents concurrently without causing inconsistency or conflicts) is supported by creating a unique index on one of the document field so that any concurrent insert or update for this document won't result in a duplicate document. 

##### Data Import and Export

MongoDB supports the db.collection.bulkWrite() method that can be used to execute a batch of write operations such as insert, update and delete at the same time. Additionally, MongoDB supports two useful tools (mongoexport and mongoimport) that can be used to migrate data between different mongodb instances. By using mongoexport or mongoimport, you can import or export data in a CSV or BSON format. 


##### Data Layout

MongoDB is schema-less and doesn't enforce documents structure. Hence data modelling is more flexible. Coming up with good data model design needs to consider the below criteria:

-  What are your data access patterns?
-  How do you query and update your data?
-  How is your data look like?
-  What are your application requirements?
-  What are the relationships between your data?

Data can be modeled based on the data relationships using the normalized model or the denormalized model. The normalized model is done by defining separate documents and using references to model the relationships between them. The denormalized model is done by embedding documents inside each other to model relationships between them. The normalized model provides more data modeling flexibility and prevent data duplication but requires many follow-up queries that reduce the performance.  The denormalized model provides better read perfromance and support for transactions, however the document size can increase to unbound limit and impact performance due to data duplication.

##### Relational data

All relationships can be either modeled using the normalized or the denormalized models. However it is recommended to model the one-to-one relationships using the denormalized model by embedding documents to provide better perfomance. The one-to-many relationships can be modeled using the denormalized model if this wont result in document growing to an unbound size which can impact perfromance. Finally, the many-to-many relationships are recommended to be modeled using the normalized model using references to prevent extensive data redundancy that can result in very large documents.

##### Normalisation/Denormalisation

MongoDB encourage denormalising the data by using the rich document structure it supports. Denormalising data in MongoDB can reduce development complexity and speed time to market as well as increasing performance. In the other hand, MongoDB supports normalised or denormalised models when representing relationships between your data as have been already explained in previous sections.

##### Referential Integrity

There is no referential integrity enforced by MongoDB

##### Nested Data

MongoDB supports very well nested structures by allowing documents to embed single or multiple documents. This feature allows for great flexibility during data modelling. 


##### Built-in query functions

MongoDB supports many built in functions that can be applied during query operations such as skip(), limit(), min(), max(), distinct and count()

##### Query


MongoDB supports one simple method db.collection.find() to search for documents inside a certain collection. This method accepts two inputs, the first input is the selection criteria for you query and the second input is an optional to specify the fields to be returned or what is called the projections. This method will return a cursor that can be used to iterator through all the returned documents. MongoDB supports parametrised and range using many query selectors such as $in, $eq, $gt, $lt, $gte, $lte, $ne and $nin. It is also supported to query arrays using the $elemMatch keyword. Finally, it is possible to query embedded documents using the dot notation.


##### Full Text Search

MongoDB supports Full-Text Search using Text Indexes. Text Indexes can be defined on any string or array of strings values in your documents that you want them to be fully searched. By defining a text index on any field, MongoDB will tokenises and stems the field's text content and creates the index accordingly. You can create only one text index per collection but it is possible to create compound Text index or even Text index the whole document. 

##### Regular Expressions

MongoDB supports regular expressions for pattern matching strings in query operations using the $regex keyword.


##### Indexing

MongoDB indexes are created using the efficient B-tree data structure to store small portion of information used to speed up queries by limiting the number of documents to be searched. These information will be stored using the B tree structure in order useful to perform range queries or sorting. MongoDB supports a variety of index types that are defined on the collection level and can apply to any field or subfield in the document such single index, compound index, Multikey index, Geospatial index, Text index, and Hash index. The index can have many properties such as Unique, Partial, Sparse and TTL.


##### Aggregation

MongoDB supports three ways to aggregate the data, either through the pipeline aggregation framework, or by using the map reduce data processing paradigm or through some supported uses-specific functions such as distinct, group and count functions. In the aggregation pipeline framework, the data is processed through multiple stages before a final results is returned. Many stages are supported such as $project, $match, $group, $sort and $sample stages. The aggregation is done using the db.collection.aggregate() method that operates on a single collection and can take advantage of the already created indexes to enhance performance. In general, using the aggregation pipeline framework is the more preferred way to do aggregation related tasks since it is less complex and more efficient than using map-reduce. However map-reduce provides some sort for flexibility since it is using custom javascript functions to do the map and reduce operations.



##### Sorting

MongoDB supports a sort() method that allows for sorting the data by some sorting fields either ascendingly or descendingly. It is recommended to create an index on the fields that will be used for sorting.


##### Document Validation Feature

Used to enforce some validation rules on the documents structure. These rules will be checked when an insert or update is performed against the collection. If the validation rules have been violated, then an error or warning will be raised depending on the validationAction option. 

##### GridFS Feature

Used to store and manage large files that are beyond the normal 16MB document size. GridFS simply store large binary files by splitting them into smaller parts called "chunks" and then store them into MongoDB. Then when you want to retrieve the file, GridFS will combine all the chunks and return the file for you. You can also perform range queries on the file chunks to retrieve only a certain part of the file.


##### Configuration

MongoDB uses a YAMLconfiguration file for each mongod or mongos instance that contains all the settings and command options that will be used by this instance. If you want to change a configuration option in the configuration file of a mongod or mongos instances, you will need to restart the instance to pick up the new changes.

##### Scalability


MongoDB supports sharding by using a sharded cluster which is composed of three components: the query routers, the configuration servers and the shards.  The query routers ate mongos instances which are used by the client applications to interact with the sharded data. Client applications can't access the shards directly and can only submit read and write requests to mongos instance. Mongos instance is the one responsible for routing these reads and writes requests to the respective shards.  The config servers holds the metadata of the sharded cluster such as the shards locations of each sharded data. Config servers contain very important information for the sharded cluster to work and if the servers are down for any reason, the sharded cluster becomes inoperable. For this reason, it is a good idea to replicate the data in the config server using a replica set configuration which allows the sharded cluster to have more than 3 config servers and up to 50 servers to ensure availability. The shards are a replica set or a single server to hold part of the sharded data. Usually, each shard is a replica set which provides high availability and data redundancy that can be used in case of disaster recovery.  MongoDB partition and distribute the data evenly based on the sharded key. To partition the data using the sharded key, MongoDB either use range based partitioning or hash based partitioning. In the range based sharding, MongoDB divides the data into ranges based on the sharded key values which provides efficient range queries but uneven distribution of the data. In the other hand, the hash based partitioning divides the data based on the value of a hash function which provides random even distribution of the data but not efficient range queries.

##### Persistency


MongoDB supports two persistent storage engines: the WiredTiger storage engine or the MMAPv1 storage engine.  Usually, persistency is supported by flushing the data from the memory to disk periodically (default is each 60 seconds). In addition, a mechanism called journalling is also used to provide more durable solution in the event of failure. MonogoDB uses journal files to store the in-memory changes and can be used later to recover the data when the server crashes before flushing data to disk files. The write operations in MongoDB are durable when journalling is enabled. Otherwise, write operations could be lost between checkpoints or flushing data to disk. So the write operation is considered durable when it has been written to the journal files in case of a single server mongod. In case of a replica set, the write operation is durable if it has been written to the journal files of the majority voting nodes.

##### Backup

MongoDB provides a tool called mongodump that can read data from the database and creates a backup BSON files. To restore the created backup files, you can use the mongorestore tool to populate the MongoDB database with the backup BSON files. After restoring the data using mongorestore, the mongod instance must rebuild all indexes. Mongodump tool can impact the mongod instance performance, so it is usually recommended to run this tool against a secondary member in a replica set. 



##### Security

MongoDB offers many security features such as Authentication, Role-Based access control, and communication encryption. Authentication used to verify the identity of clients who are trying to connect to the database. Role-Based Access Control (RBAC) used to control the access to certain resource by granting a set of permissions to the user. A resource is either a database, a collection, set of collection or a cluster. Finally, MongoDB supports the use of TLS/SSL protocols for encrypting the connections to mongod or mongos instances.

##### Upgrading

To upgrade a MongoDB instance, you should start by shutting the server instance first. After that you can either manually upgrading by downloading the new 3.2 binaries and then just replace them with the old 3.0 binaries or you can use the package managers such as apt, dnf, yum, or brew.


##### Availability

MongoDB supports data redundancy through replication. MongoDB supports a configuration called a replica set which is a group of mongod instances that use the same data set. Each replica set has only one a primary node and the rest are all secondaries.  MongoDB supports also a special type of nodes called arbiters which are used only in case that there are even number of nodes in the replica set and to be used during the voting process. The primary node is the node responsible for all write operations and records all of its operations to an operation log called oplog. The oplog is then used by secondary nodes to replicate the primary data set. The primary node is also the one responsible for read operations, the application can chose a different read concern if it needs to read data from secondary nodes but it is not recommended since the data in secondary nodes might be stale. If you want to scale reads, you can use sharding and not replication. Replication in MongoDB is mainly supported for data redundancy and availability. Secondaries first do an initial sync when the secondary member doesn't have data such as when the member is new. During the initial sync, the secondary node will clone all the databases and build all indexes on all collections. After that the secondary nodes will just do a simple sync operation to replicate the primary node data using the oplog asynchronously. MongoDB also supports that you configure a secondary node so that it can't be promoted into a primary node in case of a failure by changing it is priority to zero. If you want to use some secondary nodes for specific tasks such as reporting or backup, you can configure the nodes to be hidden by setting its priority to zero to prevent a hidden member to become primary. You can also configure a secondary node to act as a delayed replica that contains an earlier version of the data set and can be used later for rollback or running historical snapshots. Finally, MongoDB supports an automatic failover process that takes place when the primary node in a replica set isn't accessible for some reasons. If the other replica set members aren't able to communicate with the primary server for more than 10 seconds, an eligible secondary member will automatically be promoted to act as a primary node. The selection of the secondary node that will replace the failed primary node is based on a majority voting of the replica set members. 
