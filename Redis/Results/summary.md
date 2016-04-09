[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Results > Summary


Redis is an extremely fast NoSQL key-value in-memory database which stores data in different useful data structures such as strings, list, sets and sorted sets. Redis supports many powerful features like built-in persistency, pub/sub, transaction support with optimistic locking and Lua Scripting. Redis is a very good choice if you are looking for a very fast and highly scalable and available data store solution.


##### Use cases

Redis is a high performance database that can be scaled easily to hundreds of gigabytes of data and millions of requests per second. It also provides on-disk persistence and supports very unique data structures which makes it suitable for a variety of applications such as caching, as a message broker, as a chat server, in session management, as a distributed lock system, to implement queues, as a logging system, in any counting related applications,  in real time analytics, to implement leaderboards, as a voting system and many other applications.

##### Basic Concepts

Redis supports multiple useful data structures such as strings, lists, hashes, sets and sorted sets. Strings can be also integers, floating point values or even a binary data such as an image or any file. However, the size of the stored value shouldn't exceed a maximum 512MB of data. Lists stores sequence of ordered elements that follows the well-known linked list structure. The elements are added only to the head or the tail of the list which is an extremely fast operation and takes a constant time. However accessing the elements by index is not so fast and will requires an amount of work proportional to the accessed index. Hashes stores used to store complete complex objects and can be compared to the documents in a documents-based database or like a row in a relational database. Sets can be used to store unordered sequence of strings that are unique and sorted sets are a special case of the set implementation where it defines a score for each string. The score allows you to get the elements inside the sorted sets by a particular order. Also you can retrieve ordered elements in the sorted set by score range.

##### Installing

Redis can be installed on most of the popular operating systems except windows. It can easily installed by building it from source using the distribution binaries or by using many package managers such as apt-get, yum, brew , and port.

##### Query language


Redis supports a set of commands for each data type to run CRUD operations such as add, update, get or remove in the stored data. Those commands can be executed on bulk and a partial transaction is supported. Executing these commands can be done using the built-in client "redis-cli" or by using one of the supported driver clients specific for many programming languages. Redis has a relatively short list of commands that can be easily learned in few hours.



##### Transaction support

Redis is a single threaded application only one command can be executed at a time. So the idea behind transactions support in Redis is to wrap the transaction commands and execute them all as a single command. This is done by using MULTI/EXEC commands, you start the transaction by using MULTI command, then all the following commands will be queued. After all transaction commands are queued, they will be executed all as a single command using the EXEC command.  The transaction commands will be either executed together or none will be executed. However the problem occurs when any one of the transaction commands are mistakenly executed (not syntax mistakes) due to some programming bug. In this case unfortunately Redis doesn't provide any way for a Rollback as in traditional databases. Rollback isn't supported due to reasons related to performance.   To avoid race conditions that will happen when another client tries to modify the same value used in a transaction, optimistic locking is provided using WATCH/UNWATCH commands. Finally, Redis support scripting using LUA which is transactional by definition since it can be executed as a single command. Hence Lua scripting can be used to support transactions.




##### Data Import and Export

Redis supports a pipe mode that was designed in order to perform mass insertion. You just put all the data you want to insert in a file and then perform mass insertion using "redis-cli --pipe". Pipelining is also supported in Redis which is used to batch multiple commands and send them together to the server at once without waiting for their replies. At the end you can read the replies in a single step. Pipelining can improve system performance since the round trips between client and server are reduced.


##### Data Layout

In Redis there is no schema and hence no need for data model design. We just need to think about our underline data structure that we need to store. Then we need to think about which data types we need to use in order to represent this data. An example would be to model an article voting system. We would store the article object in Hash. Then we can store the list of articles ordered by votes in a sorted sets where score is the number of votes. Finally a normal set can store the name of the users who have voted for a particular article.

##### Relational data

Using Redis for complex relational data isn't usually recommended, however Redis supports modelling data relationships using sets and sorted sets. Generally, a one-to-one and one-to-many relationships are modelled using a single set but modeling many-to-many relationships using two sets. Join-like operations are then possible using sets intersect and union commands such as SDIFF, SINTER, SUNION, ZINTERSTORE, and ZUNIONSTORE.
  

##### Normalisation/Denormalisation

Denormalisation can be used in Redis wherever it is needed if it will give better performance or model data relationships.

##### Referential Integrity

Since Redis isn't a relational database, referential integrity is not maintained by Redis, and must be enforced by the client application. Also there is no concept of Foreign key for the same reasons.

##### Nested Data

Redis doesn't support nested structures. For instance, Hash, lists and sets internal elements can only be of a String data type and can't embed other data types. However you can still store in the string data type any stream of bytes which can be JSON data but that wont be helpful since you can't query this nested data. 

##### Built-in query functions

Count function is supported in Redis using different commands for each data type such as LLEN, SCARD, and ZCARD. Min and Max functions can be achieved using a sorted set using commands such as ZRANGEBYSCORE, and ZREVRANGEBYSCORE. Finally, there is no support for aggregate functions such as sum and average and these functions need to be handled manually in the client application.


##### Query

Redis is a key-value database which means you can query your data easily using the keys. A secondary or compound index-like can be created and maintained manually to support querying using fields other than the key. Finally, Range queries are supported using sorted sets. 


##### Full Text Search
Redis doesn't support full-text search internally, but some search engines can be used with Redis such as elastic search and Apache Solr.

##### Regular Expressions

Redis supports some search commands that uses regular expressions such as KEYS which searches all keys in a Redis instance. Additionally Redis supports the SCAN command which is used to search and iterate through all keys and values (SSCAN, HSCAN, and ZSCAN are the same but specific for each data type). KEYS and SCAN can use global-style regular expression patterns.


##### Indexing

Redis is a key-value store, the key acts as the primary index in all data structures. Redis doesn't support internally other type of indexes such secondary and compound indexes. However, secondary and compound indexes can be implemented and maintained manually by the clients using sets and sorted sets.  Although creating secondary indexes and compound indexes is still possible somehow using sets and sorted sets, maintaining theses indexes manually at each write operation can be difficult and is prone to human error. 

##### Grouping and Filtering

In general, grouping and filtering data in Redis is done using the sets and soted sets data structures. To group your data in Redis, the set data structure is the best candidate since you will be able to easily insert elements from the same group type as members of the set using sets and sorted sets. To filter data in Redis, you can use the intersection, union, and difference functionalities provided by the set and sorted sets. 

##### Sorting

Redis supports a sort command that can be used to sort most of Redis data structures such as lists, sets and sorted sets. This sort command can sort your data ascendingly, descendingly or alphabetically. You can also sort by a specific pattern or limit the returned data. Another useful feature of the sort command, is that you can also sort by external keys.

##### Lua scripting

Redis allows you to execute Lua scripts inside the Redis server. Scripting Redis with Lua is useful and can be used to avoid some common pitfalls that slow down development or reduce performance. To load the script to Redis server you can use either EVAL or EVALSHA commands which will evaluate the scripts using the Lua interpreter and load it. By using EVAL you can simply load a Lua program without defining a Lue function and pass arguements to the Lua program as redis keys.  The script is executed in Atomicity way which makes it useful for transaction related tasks.


##### Pub/Sub

This is a special feature of Redis that can be used to implement applications such as a chat server. The idea is mainly characterised by listeners who can subscribe to certain channels to receive real time messages. The publishers can send binary messages of type string to channels without having any knowledge of whether there are subscribers or not. This feature is following the publish/subscribe messaging paradigm. 


##### Expire

In Redis you can set a timeout on a key. It means that the key will be automatically deleted after the timeout period expires. This feature makes Redis a good database to implement cashing and similar applications.

##### Configure Redis as a cache

If you plan to use Redis as a cache system, then instead of expiring keys manually you can just let Redis take care of this automatically. Redis provides a way to easily configure your Redis server to act automatically as a cache and use one of the popular eviction algorithms such as the LRU algorithm which makes it act like memcached database.


##### Configuration

Redis can start without a configuration file and then it will take its built-in default configurations. Redis stores its configurations inside a file called redis.config and you can also pass configuration parameters via the command line. Configuring Redis while the server is running is also possible without the need to stop or restart the server using CONFIG GET , and CONFIG SET.


##### Scalability

Scaling reads in Redis is done using replications where slave instances can share the read load. Scaling writes is supported using sharding. The important question about sharding is who will shard the data. In Redis, the partitioning can be done in the client side where the clients directly select the right instance to be used to write or read a certain key. Most of the clients or drivers that are provided by Redis have implementations for partitioning. Another implementation for partitioning is by using a proxy which means that clients send request to a proxy that is able to interact with Redis and will forward the clients' requests to the right Redis instance then send back the reply to the clients. A famous proxy used by Redis and Memcached is [Twemproxy](https://github.com/twitter/twemproxy). Finally it is also possible to use an implementation for partitioning called query routing where you just send your requests to a random instance that will forward your request to the right Redis instance. Starting from Redis version 3.0, Redis cluster is the preferred way to get automatic partitioning and high availability. Redis cluster uses a hybrid implementation of query routing and some clients side implementation. 


##### Persistency


Redis supports two ways to persist data on disk. The first option is called snapshotting or RDB which takes a snapshot of the current data periodically (based on pre-configured value) and store it on disk. The second option is called append-only file or AOF which simply works by logging the operations that modifies the data (write operations) into a log file. Processing this log file later if needed will produce the same dataset that was in memory. Additionally depending on your durability requirements, you need to set the fsync options as explained below:

- "fsync every" when new command is logged to the AOF. This option is used if you want to have a full durability support but it is also a very slow option. 
 - "fsync every second". This is the default configuration which will provide a good durability option but with a 1 second data lose risk in case of a disaster.This option is fast enough.
 - "Never fsync", using this option will let the operating system handle the fsync which can be very unsafe in term of durability. On the other hand, this option is very fast.


Atomicity can be guaranteed by executing a group of commands either using MULTI/EXEC blocks or by using a Lua script.
Consistency is guaranteed by using the WATCH/UNWATCH blocks.
Isolation is always guaranteed at command level, and for group of commands it can be also guaranteed by MULTI/EXEC block or a Lua script.
Full Durability can be also guaranteed when using AOF with executing fsync with each new command as explained before.

##### Backup

Backup in Redis is basically the snapshots that were taken of your dataset if the snapshot option is enabled. Your data will be stored in an RDB file called dump.rdb file in your redis directory. To restore your data, you just need to move this dump file to your Redis directory and then start up your server. To have a better backup, you can use snapshotting with replication so that you will have the same copy of your data in all your instances including masters and slaves. This will ensure that your data is save in case the master server crashed completely



##### Security

Redis doesn't provide any access control and this should be provided by a separate authorization layer.  However Redis provides an authentication mechanism that is optional and can be turned on from redis.conf. Redis supports the BIND command to allow only specific IP addresses to access the Redis server for better security. Redis doesn't also support any encryption mechanism and this should also be implemented using a separate layer like using encryption mechanisms such as SSL proxy. The "rename-command” used to rename the original commands which reduces the risk that might happen if unauthorised clients access your server. Finally,  NoSQL injection attacks are impossible since Redis protocol has no concept of string escaping. 


##### Upgrading

Redis doesn't support online upgrades (server must restart and the clients can't connect to it during the upgrade window). A workaround for online upgrade is to start a slave server and direct all the clients to it while you do an upgrade to the master. After the upgrade is finished in the master, we can stop the slave server and then redirect the clients requests again to the master server.


##### Availability

Redis provides a distributed system called Redis Sentinel to guarantee high availability. Redis Sentinel can resist certain types of failures automatically without any human intervention. Redis Sentinel can be used also for other tasks such as monitoring, as a configuration provider for clients and as a notification system. By Monitoring we mean that Redis Sentinel will monitor your master and slave instances and check if they are working as expected. It will also send notifications to the admin in case anything went wrong. Clients can also contact the Redis Sentinel to get any configurations parameters for a certain Redis instance such as address and port. Finally Redis Sentinel will start a failover process in case the master is not working to promote one of the slaves to replace it and reconfigure the other slaves to contact this new master.

An important note is that in order to get the best robustness out of Redis Sentinel, you should configure it in such a way that it will not be a single point of failure. This means that you use multiple instances in separate machines or virtual machines that fail independently which can cooperate together to handle Redis failure detection and recovery.  You need at least three Redis Sentinel instance to guarantee its Robustness. 