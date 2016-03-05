
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


##### Query



##### Full Text Search


##### Regular Expressions



##### Indexing



##### Aggregation


##### Sorting



##### Document Validation Feature



##### GridFS Feature



##### Configuration


##### Scalability



##### Persistency




##### Backup




##### Security


##### Upgrading


##### Availability

