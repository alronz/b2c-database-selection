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
