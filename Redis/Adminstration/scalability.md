#### [back](admin_main.md)

In this section we will talk about how good and easy is Redis when it comes to scalability. The section will be divided into four parts to explain how to scale reads, writes , configurations, and complex queries.

### Scaling Reads

When your system grows bigger and the system read throughput increases more than what Redis server can handle, then you must scale your server to ensure that reads are as fast as possible.  The simplest available to Redis is to add read-only slave servers or to use the Replication option that is supported by Redis. The replications is used mainly for scaling reads but it can be also used for data redundancy and disaster recovery.
#### Replication 
Redis supports a very simple to use master-slave replication where you can configure as many as you needed read-only slave Redis servers to be an exact copy of the master server. The master server will replicate its dataset frequently as configured with its slave and this can be also done asynchronous where slaves will periodically acknowledge how much data was successfully processed from the replication stream. The slave can also have other slaves connected to it in a graph-like structure. Only the master will be accept write operations and all other slaves will be used just as read-only servers and will throw errors if you try to write to them. Master will be also available during the replications process since it is a non-blocking operation. However for slaves they can be still available during the replication but will server requests with the old dataset only. 
#####  Configuring Replication
Redis replication process between master and slave is simple, the master receives a SYNC command then it saves the current dataset to disk as RDB file and during saving the file any write commands received by clients will logged. When the RDB file is saved to disk, the master will send the file to all connected slaves with the logged commands. Slaves will receive the file and save it on disk then it will loads it in memory. Then the after receiving the logged commands , they will apply the commands to the new loaded dataset.  If the link between the master and its slave goes down for any reason , a full synchronisation will be done once reconnected. To configure a Redis server to be a slave, just run add the below line to the configuration file of the slave server:


````
slaveof 192.168.1.1 6379
````

The above IP is the IP of your master server. Also you can issue the command SLAVEOF from the slave server in the same way to make it listen to a master server. If you want to stop the replication and make the slave server acts as a master, you can issue the below command:

````
SLAVEOF NO ONE````You can also configure the slave to use a certain credentials when syncing up with the master server. You can use the below command:
````config set masterauth <password>````We will discuss later in the [availability section](link to availability) how we can use Redis Sentinel to handle master or slave failures. 
### Scaling Writes


If your data grows beyond your memory or when you want to increase the overall system performance, you might need to scale up your Redis database by sharding your data among multiple Redis instances. This will allow for much larger database which will be the sum of the memory of all Redis instances.  In addition, the computational power and network bandwidth of your database will increase because you are using now multiple servers with multiple cores with multiple network adapters.  

There are different methods available to partition your data in Redis. The simplest way is the range partitioning which is done by mapping your data by range to different Redis instances. For example, if you have five instances R1 and R2, then you could say that users with ID 1 to 100 works with R1 and from 101 to 200 works only with R2 and so forth. This method is easy but not so practical because of the cost of maintaining a mapping table. The other most used method is by hashing objects keys in Redis to certain server instances. For instance we take the string key of a certain value and by using a particular hash function, we get a hash number that can by mapped to a certain instance by taking the modulo of this number e.g hashing a key with name "keyValue" will generate 23341986 then if you take the modulo like 23341986 % 2, you get 2 which is the Redis instance 2. However the important question is who does this partitioning.

The partitioning can be done in the client side where the clients directly select the right instance to be used to write or read a certain key. Most of the clients or drivers that are provided by Redis have implementations for partitioning. Another implementation for partitioning is by using a proxy which means that clients send request to a proxy that is able to interact with Redis and will forward the clients' requests to the right Redis instance then send back the reply to the clients. A famous proxy that is used by Redis and Memcached is [Twemproxy](https://github.com/twitter/twemproxy). Finally you can also use an implementation for partitioning called query routing where you just send your requests to a random instance that will forward your request to the right Redis instance. 

Before Redis version 3.0, users usually tend to use client side implementations for partitioning their data to multiple Redis instances. But when Redis cluster was introduced, it is now the preferred way to get automatic partitioning and high availability. Redis cluster uses a hybrid implementation of query routing and with some clients side implementation. I will explain in the following section how to use Redis cluster to do partitioning.

#### Redis Cluster

Redis cluster was introduced to allow users to do data partitioning automatically which much easier and convenient for the user. Redis cluster doesn't only provide a data partitioning capabilities but also allows for some degree of availability during partitioning. This means that that the Redis database will continue to be operational even if some of the instances are experiencing failures.

The concept of Redis cluster is simple. The cluster will have a total of 16384 hash slots and will be distributed among all the instances in the cluster so that each instance will have a subset of these hash slots. Then each key will be mapped to its hash slot by simply taking the CRC16 of the key modulo 16384. Using this hash slots allow for easier management of the nodes inside the cluster. So you can easily add or remove nodes from the cluster by just either distributing its hash slots to the other nodes in case we want to remove it or by just moving a subset of the hash slots to it in case of adding. The node that has an empty set of hash slots will be automatically deleted by Redis cluster. Redis cluster also supports operations on multiple keys as long as all the keys are in the same hash slot by using a concept called hash tags.

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

Please have a look to the README file inside the same directory where the scrip exists. After configuring Redis Cluster, you can play with it using redis-cli command line utility. Examples of possible interaction is shown below:


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

Please note that using at least one slave for each master node is the best configuration that will help the Redis cluster to stay operational in case of failure. Handing failures will be discussed [later](link to this section).


### Scaling Configurations

Redis Sentinel acts as a configuration provider for clients and can be used in case you have a system with many master and slave nodes where managing configurations can be a great challenge. 






























