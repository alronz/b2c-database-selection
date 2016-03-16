In this section, I will talk about the strengths and weaknesses of Redis and when using it makes sense.


##### Strengths 

Redis has many advantages that makes it suitable for many use cases. Below I have summarised the main ones. 

###### Very fast in-memory database

Because of it is architecture, Redis is Lightening fast. 

###### Complex data structures

###### High availability

###### On-Disk persistance

###### Lua scripting

###### Built-in replication and automatic sharding

###### Configured as a cache

###### Pub/Sub


###### Single threaded with transaction support
No Rollback


##### Weaknesses

###### Memory limit

###### Persistence

###### Query and Aggregation

- no internal full Text Search
- Relational data and complex queries

###### Indexing


###### Security




##### Summary



Best used: For rapidly changing data with a foreseeable database size (should fit mostly in memory).

For example: To store real-time stock prices. Real-time analytics. Leaderboards. Real-time communication. And wherever you used memcached before.


best suited to relatively small data sets,If you want to store huge datasets then the balance between speed and cost will probably end up favoring cost.
On the other hand, for many uses, the amount of RAM available is entirely sufficient.