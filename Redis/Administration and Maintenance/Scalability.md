

### [Redis ](../Redis.md) > [Administration and Maintenance](Administration and Maintenance.md) > Scalability
___



In this section we will talk about scalability in Redis. The section will be divided into four parts to explain how to scale reads, writes, configurations, and complex queries.

### Scaling Reads

When your system grows bigger and the system read throughput increases more than what Redis server can handle, then you must scale your server to ensure that reads are as fast as possible.  The simplest way is to use the replication option that is supported by Redis by adding read-only slave servers. The replication is used mainly for scaling reads but it can  also be used for data redundancy and disaster recovery.
#### Replication 
Redis supports a very simple to use master-slave replication option where you can configure as many as you need read-only slave servers to act like an exact copy of the master server. The master server will replicate its dataset frequently as configured with its slave. The replication process is done asynchronously where slaves periodically acknowledge how much data was successfully processed from the replication stream. The slaves can also have other slaves connected to them in a graph-like structure. Only the master will accept write operations and other slaves will be used just as read-only servers. The slave servers will throw errors if you try to write to them. Master instance will be available during the replication process since it is a non-blocking operation. However slaves can still be available during the replication but will serve requests with the old dataset only. 
#####  Configuring Replication
Redis replication process between master and slave is simple. The master receives a SYNC command then it saves the current dataset to disk as RDB file. During saving the file, any write commands received by clients will be logged down to a file. Later when the RDB file is saved to disk, the master will send the file to all connected slaves along with the log file. Slaves will receive the RDB file and save it on disk. Then slaves will load the RDB file into memory and apply the log file commands to the new loaded dataset.  If the link between the master and its slave goes down for any reason, a full synchronisation will be done once reconnected. To configure a Redis server to be a slave, just run the below command on the slave instance:


````
slaveof 192.168.1.1 6379
````

The above IP is the IP of the master server. If you want to stop the replication and make the slave server acts as a master, you can issue the below command:

````
SLAVEOF NO ONE````You can also configure the slave to use certain credentials when syncing up with the master server. You can use the below command:
````config set masterauth <password>````We will discuss later in the [availability section](Availability.md) how we can use Redis Sentinel to handle master or slave failures. 
### Scaling Writes


If your data grows beyond your memory or when you want to increase the overall system performance, you might need to scale up your Redis database by sharding your data among multiple Redis instances. This will allow for much larger database which will be the sum of the memory of all Redis instances.  In addition, the computational power and network bandwidth of your database will increase because you are using now multiple servers with multiple cores with multiple network adapters.  

There are different methods available to partition your data. The simplest way is range partitioning which is done by mapping your data by range to different Redis instances. For example, if you have two instances R1 and R2, then you could say that users with ID 1 to 100 assigned to R1 and from 101 to 200 assigned with R2 and so forth. This method is easy but not so practical because of the cost of maintaining a mapping table and the data distribution in uneven. The other method is by using a hash function that will hash the keys and decide where to distribute the data.

The important question is who does the partitioning. In Redis, the partitioning can be done in the client side where the clients directly select the right instance to be used to write or read a certain key. Most of the clients or drivers that are provided by Redis have implementations for partitioning. Another implementation for partitioning is by using a proxy which means that clients send request to a proxy that is able to interact with Redis and will forward the clients' requests to the right Redis instance then send back the reply to the clients. A famous proxy used by Redis and Memcached is [Twemproxy](https://github.com/twitter/twemproxy). Finally it is also possible to use an implementation for partitioning called query routing where you just send your requests to a random instance that will forward your request to the right Redis instance. 

Before Redis version 3.0, users usually tend to use client side implementations for partitioning their data to multiple Redis instances. But when Redis cluster was introduced, it is now the preferred way to get automatic partitioning and high availability. Redis cluster uses a hybrid implementation of query routing and some clients side implementation. I will explain in the following section how to use Redis cluster to do partitioning.

#### Redis Cluster

Redis cluster was introduced to allow users to do data partitioning automatically which is much easier and convenient. Redis cluster doesn't only provide a data partitioning capabilities but also allows for some degree of availability during partitioning. This means that the Redis database will continue to be operational even if some of the instances are experiencing failures.

The concept of Redis cluster is simple. The cluster will have a total of 16384 hash slots and will be distributed among all the instances in the cluster so that each instance will have a subset of these hash slots. Then each key will be mapped to its hash slot by simply taking the CRC16 of the key modulo "16384". Using this hash slots allow for easier management of the nodes inside the cluster. So you can easily add or remove nodes from the cluster by just either distributing its hash slots to the other nodes in case we want to remove it or by just moving a subset of the hash slots to it in case of adding. The node that has an empty set of hash slots will be automatically deleted by Redis cluster. Redis cluster also supports operations on multiple keys as long as all the keys are in the same hash slot by using a concept called hash tags.

##### Configuring Redis Cluster

Configuring Redis cluster isn't that easy and it would be better if you go through the Redis [cluster specifications](http://redis.io/topics/cluster-spec) to do a serious configuration or deployment in a production environment. Below I will explain the configuration parameters related to Redis cluster that can be included in your redis.conf file.

cluster-enabled is used to include this instance as a cluster instance otherwise it will act as a standalone instance.
cluster-config-file is used to mention where Redis cluster can save its automatically generated configuration file for this instance.
cluster-node-timeout  is the maximum amount of time that a Redis cluster instance can be down.
cluster-slave-validity-factor is simply a factor which will be multiplied by the timeout to  determine how much time it will take for the slave to fail when the link to the master is down. 

To simplest way to configure Redis cluster is to use the create-cluster script which can be founded under utils/create-cluster directory in the Redis distribution. This script is just a simple shell script that will configure a Redis cluster to start 6 nodes with 3 master nodes and 3 slaves. To run the script just run the below commands:

````
create-cluster start
create-cluster create
````

You will then get a message to accept the cluster layout, just reply with Yes. To stop the cluster you can run:


````
create-cluster stop
````

Please have a look at the README file inside the same directory where the scrip exists. After configuring Redis Cluster, you can play with it using redis-cli command line utility. Examples of possible interaction is shown below:


````
# interacting with the instance with port 7000
$ redis-cli -c -p 7000
redis 127.0.0.1:7000> set foo bar
-> Redirected to slot [12182] located at 127.0.0.1:7002
OK
redis 127.0.0.1:7002> set hello world
-> Redirected to slot [866] located at 127.0.0.1:7000
OK
redis 127.0.0.1:7000> get foo
-> Redirected to slot [12182] located at 127.0.0.1:7002
"bar"
redis 127.0.0.1:7000> get hello
-> Redirected to slot [866] located at 127.0.0.1:7000
"world"
````

Please note that using at least one slave for each master node is the best configuration that will help the Redis cluster to stay operational in case of failure. 


### Scaling Configurations

Redis Sentinel acts as a configuration provider for clients and can be used in case you have a system with many master and slave nodes where managing configurations can be a great challenge. 






























