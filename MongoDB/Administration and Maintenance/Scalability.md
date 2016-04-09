[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Administration and Maintenance > Scalability

As your data grows, your sever won't be able to handle the high rate of queries and the larger sets will exceed the server storage capacity. To address these issues, you will have to do either a vertical or horizontal scaling. You can scale your server vertically by adding more CPUs and storage resources but there is always a limit to what you can add to a single server machine. In the other hand, you can scale horizontally by distributing your data over multiple servers which is also called Sharding. Each shard will act as a separate database and all the shards make up the complete logical database. By using sharding, you can meet the demands of the high throughput read/write operations as well as the very large data sets.


Fortunately, MongoDB supports sharding by using a sharded cluster which is composed of three components: the query routers, the configuration servers and the shards. In the following sections, I will explain these components in more details. Sharding in MongoDB is done only on the collection level. You can also either shard one collection, multiple collections or the whole data as you choose.


![image](https://docs.mongodb.org/manual/_images/sharded-cluster.png)


##### Query Routers

These are mongos instances which are used by the client applications to interact with the sharded data. Client applications can't access the shards directly and can only submit read and write requests to mongos instance. Mongos instance is the one responsible for routing these reads and writes requests to the respective shards.  

Mongos has no persistent state and knows only what data is in which shard by tracking the metadata information stored in the configuration servers. 

Mongos performs operations in sharded data by broadcasting to all the shards that holds the documents of a collection or target only shard based on the shard key. Usually, multi-update and remove operations are a broadcast operations. 

In general, most sharded clusters contain multiple mongos instances to divide the client requests load, but you also use only a single instance.


##### Config Servers

The config servers holds the metadata of the sharded cluster such as the shards locations of each sharded data. Config servers contain very important information for the sharded cluster to work and if the servers are down for any reason, the sharded cluster becomes inoperable. For this reason, it is a good idea to replicate the data in the config server using a replica set configuration which allows the sharded cluster to have more than 3 config servers and up to 50 servers to ensure availability.

MongoDB stores the sharded cluster metadata information in the config servers and update this information whenever there is a chunk split or chunk migration. A chunk split happens when chunk's data grows beyond the chunk size which will make the mongos instance split this chunk into halves. This can lead to an unevenly data distribution among shards which starts the balancing process that leads to chunk migration. A chunk migration is the process of moving one chunk from a particular shard to another to achieve even data distribution among shards.

![image](https://docs.mongodb.org/manual/_images/sharding-splitting.png)


![image](https://docs.mongodb.org/manual/_images/sharding-migrating.png)



##### Shards


The shards are a replica set or a single server to hold part of the sharded data. Usually, each shard is a replica set which provides high availability and data redundancy that can be used in case of disaster recovery. 

In any sharded cluster, there is a primary set that contains the unsharded collections. 


![image](https://docs.mongodb.org/manual/_images/sharded-cluster-primary-shard.png) 


To find out which shard is a primary in a sharded cluster, you can run the mongo shell command sh.status().


##### Data Distribution in a Sharded Cluster

First to shard the data, a shard key needs to be selected. The sharded key should be either a single indexed field or a compound index. Mongodb then divid the sharded key values evenly over the shards as chunks.

To ensure that the data in the different shards are distributed evenly. MongoDB use a splitting and a balancing background processes. The splitting process splits the chunks data whenever the data grows beyond a pre-defined chunk size. And the balancing process moves the chunks between different shards whenever the data in unevenly distributed. 

The splitting and balancing processes usually triggered when a new data is added or removed, and when a new shard is added or removed. 


For a step by step tutorial on how to deploy a sharded cluster, please have a look at [MongoDB documentation.](https://docs.mongodb.org/manual/tutorial/deploy-shard-cluster/)



##### Data Partitioning

As explained previously, MongoDB partition the data based on the sharded key. To partition the data using the sharded key, MongoDB either use range based partitioning or hash based partitioning as explained below:


###### Range Based Sharding

MongoDB divides the data into ranges based on the sharded key values. For instance, if we have a numeric sharded key values, we will divid the data based on the possible range of this numeric value. Then MongoDB partitions the data based on the value of the sharded key  and distribute it to the shard responsible for that value range as shown below:


![image](https://docs.mongodb.org/manual/_images/sharding-range-based.png)


Range based sharding is better for range queries because MongoDB will check which shards are within the requested range. However, range based sharding can result in an uneven distribution which is not good for scalability. 


###### Hash Based Sharding

MongoDB supports also Hash Based Sharding which compute a hash value for the sharded key and then use this hash value to select the shard. This ensures better data distribution where data is evenly distributed across the cluster. However it is not efficient when it comes to range queries since the data is randomly distributed across the sharded cluster based on the hash function. An example is shown below:


![image](https://docs.mongodb.org/manual/_images/sharding-hash-based.png)







