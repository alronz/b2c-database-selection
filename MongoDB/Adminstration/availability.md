#### [back](../admin_main.md)

Availability measures the percentage of time an application is working for the client applications. It is quite common, that with time the database infrastructures suffer some hardware failures that might impact the availability of the database. MongoDB protects itself from such unavailability by supporting features to enable data redundancy through replication.  In the following section, I will talk about how replication is supported in MongoDB and how is the failover process is usually handled.



#### Replication

MongoDB supports a configuration called a replica set which is a group of mongod instances that use the same data set. Each replica set has only one a primary node and the rest are all secondaries. 

![image](https://docs.mongodb.org/manual/_images/replica-set-read-write-operations-primary.png)

MongoDB supports also a special type of nodes called arbiters which are used only in case that there are even number of nodes in the replica set. Even number of nodes in a replication set can prevent a successful voting process which is needed in case the primary isn't working. The arbiter node doesn't contain any data and can't be selected as a primary node. The main task of an arbiter is to add one vote which makes the number of votes an odd number to allow for a successful majority voting in case of primary failure.

![image](https://docs.mongodb.org/manual/_images/replica-set-primary-with-secondary-and-arbiter.png)

The primary node is the node responsible for all write operations and records all of its operations to an operation log called oplog. The oplog is then used by secondary nodes to replicate the primary data set. The primary node is also the one responsible for read operations, the application can chose a different read concern if it needs to read data from secondary nodes but it is not recommended since the data in secondary nodes might be stale. If you want to scale reads, you can use sharding and not replication. Replication in MongoDB is mainly supported for data redundancy and availability. 


Secondaries first do an initial sync when the secondary member doesn't have data such as when the member is new. During the initial sync, the secondary node will clone all the databases and build all indexes on all collections. After that the secondary nodes will just do a simple sync operation to replicate the primary node data using the oplog asynchronously. 


MongoDB also supports that you configure a secondary node so that it can't be promoted into a primary node in case of a failure by changing it is priority to zero. 

````
cfg.members[2].priority = 0
````

If you want to use some secondary nodes for specific tasks such as reporting or backup, you can configure the nodes to be hidden by setting its priority to zero to prevent a hidden member to become primary and setting the hidden flag to true as seen below:

````
{
  "_id" : <num>
  "host" : <hostname:port>,
  "priority" : 0,
  "hidden" : true
}
````



The hidden member can still participate in the voting process in case of primary failure.

![image](https://docs.mongodb.org/manual/_images/replica-set-hidden-member.png)



You can also configure a secondary node to act as a delayed replica that contains an earlier version of the data set and can be used later for rollback or running historical snapshots. This can be done by setting priority to zero to prevent the node from becoming a primary node and setting the hidden flat to true and setting the delay time as seen from the configuration below:


````
{
   "_id" : <num>,
   "host" : <hostname:port>,
   "priority" : 0,
   "slaveDelay" : <seconds>,
   "hidden" : true
}
````


![image](https://docs.mongodb.org/manual/_images/replica-set-delayed-member.png) 



#### Failover

MongoDB supports an automatic failover process that takes place when the primary node in a replica set isn't accessible for some reasons. If the other replica set members aren't able to communicate with the primary server for more than 10 seconds, an eligible secondary member will automatically be promoted to act as a primary node. The selection of the secondary node that will replace the failed primary node is based on a majority voting of the replica set members. 

![image](https://docs.mongodb.org/manual/_images/replica-set-trigger-election.png)

It is always recommended, to put all members that participate on the majority voting and all the members that can become primary in the same facility to prevent any network partition problems from impacting the failover process.










