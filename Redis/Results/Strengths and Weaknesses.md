


### [Redis ](../Redis.md) > [Results](Results.md) > Strengths and Weaknesses
___

In this section, I will talk about the strengths and weaknesses of Redis and when using it makes sense.


##### Strengths 

Redis has many advantages that makes it suitable for many use cases. Below I have summarised the main ones. 

###### Very fast in-memory database

Because of it is architecture and like most in-memory databases, Redis is Lightening fast. Redis is a high performance database that can be scaled easily to hundreds of gigabytes of data and millions of requests per second.

###### Complex data structures

Redis doesn't only support the String data type, rather it supports more complex data structures such as lists, hashes, sets and sorted sets. Strings can be also integers, floating point values or even a binary data such as an image or any file. This makes Redis suitable for many use cases such as to implement queues, and voting systems.

###### High availability

Redis provides a master-slave distributed system called Redis Sentinel to guarantee high availability. Redis Sentinel can resist certain types of failures automatically without any human intervention. Redis Sentinel will also start a failover process in case the master is not working to promote one of the slaves to replace it and reconfigure the other slaves to contact this new master.

###### On-Disk persistance

Redis supports two ways to persist data on disk. The first option is called snapshotting or RDB which takes a snapshot of the current data periodically (based on pre-configured value) and store it on disk. The second option is called append-only file or AOF which simply works by logging the operations that modifies the data (write operations) into a log file. 

###### Lua scripting

Redis allows you to execute Lua scripts inside the Redis server. Scripting Redis with Lua is useful and can be used to avoid some common pitfalls that slow down development or reduce performance.

###### Built-in replication and automatic sharding

Redis has a built in replication that is used to scalability as well as for availability. Redis can scale reads by allowing the slave instances to share the read load. Additionally, replication is used for fault tolerance and disaster recovery. Scaling writes is supported using automatic sharding that is provided by Redis cluster.

###### Configured as a cache

Redis can be easily configured to act automatically as a cache and use one of the popular eviction algorithms such as the LRU algorithm which makes it act like memcached database.


###### Pub/Sub

This is a special feature of Redis that uses the publish/subscribe messaging paradigm and it is suitable for implementing chatting related use cases.


###### Single threaded with transaction support

Redis is a single threaded application only one command can be executed at a time. So the idea behind transactions support in Redis is to wrap the transaction commands and execute them all as a single command.  Redis doesn't support Rollback as in relational databases and optimistic locking can be used to avoid race conditions that will happen when another client tries to modify the same value used in a transaction.


##### Weaknesses

Below are the main weaknesses of Redis:

###### Memory limit

Redis is an in-memory database, which means that the whole dataset should reside in the memory (RAM). This can be costly if you are planning to have large datasets.

###### Persistence

Persistence can impact performance since Redis will use memory dump to create snapshots used for persistency. Depending on your configuration of the fsync linux command, taking snapshots can slow the database down.

###### Query and Aggregation

Redis isn't intended for rich queries since it is a key-value database. There is no internal full-text search support and it is difficult to model relationships using Redis. Additionally, aggregate functions such as sum, average aren’t supported and need to be handled in the client side. Indexes aren't supported internally and "sets" can be used as a workaround but maintaining them manually can be very complex. Range queries are also supported by manually maintaining some "sets". Finally, Redis doesn't support any query language (only commands are used) and there is no support for ad-hoc queries like in relational database. 



###### Security

Redis supports only basic security options. Redis doesn't provide any access control and this should be provided by a separate authorization layer.  Redis doesn't also support any encryption mechanism and this should be implemented also using a separate layer such as SSL proxy. 


##### Summary

Redis is a very fast in-memory database that is suitable for limited use cases and can't be used as a general domain database. It is best suitable for applications that have a foreseeable dataset size since Redis's dataset can't greater than the memory. Of course, it is possible to scale out Redis easily to support very large datasets but this can be very costly. Redis is suitable for applications with reasonable dataset size that requires high speed or near real-time performance such as real-time analytics. Additionally due to its unique internal data structures, Redis can be used to implement applications such caching, session management, chat server,  and queue systems.  Redis isn't suitable for applications that are having heavily related data and that is intended to serve complex or rich queries in the future.