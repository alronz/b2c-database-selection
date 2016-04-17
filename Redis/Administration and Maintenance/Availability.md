


### [Redis ](../Redis.md) > [Administration and Maintenance](Administration and Maintenance.md) > Availability

___


Redis provides a distributed system called Redis Sentinel to guarantee high availability. Redis Sentinel can resist certain types of failures automatically without any human intervention. Redis Sentinel can be used also for other tasks such as monitoring, as a configuration provider for clients and as a notification system. By Monitoring we mean that Redis Sentinel will monitor your master and slave instances and check if they are working as expected. It will also send notifications to the admin in case anything went wrong. Clients can also contact the Redis Sentinel to get any configurations parameters for a certain Redis instance such as address and port. Finally Redis Sentinel will start a failover process in case the master is not working to promote one of the slaves to replace it and reconfigure the other slaves to contact this new master.

An important note is that in order to get the best robustness out of Redis Sentinel, you should configure it in such a way that it will not be a single point of failure. This means that you use multiple instances in separate machines or virtual machines that fail independently which can cooperate together to handle Redis failure detection and recovery.  You need at least three Redis Sentinel instance to guarantee its Robustness. 

Running Redis Sentinel is easy and can be done using the below command:

````
redis-server /path/to/sentinel.conf --sentinel
````

However before running Redis Sentinel, you should make sure that you have already created a configuration file. Otherwise Redis Sentinel will fail to start. In the configuration file, you need to set some minimal configuration parameters such as:

````
sentinel monitor <master-group-name> <ip> <port> <quorum>
````

in the above example, we are asking Redis Sentinel to monitor a particular master instance. You need to repeat this configuration for each master instance in your system. The slaves are discovered automatically and no need to configure them. The <quorum> above is the number of Redis Sentinel instances that need to agree when detecting a failure in master server. 

There are other configuration parameters for each master node which follows the below pattern:

````
sentinel <option_name> <master_name> <option_value>
````

Examples of the options are down-after-milliseconds and parallel-syncs. The option down-after-milliseconds is used to define the time in milliseconds for the Redis Sentinel to ping a certain Redis instance to consider this instance to be down. The parallel-syncs option is the number of slave nodes that will be reconfigured with the new selected master node at the same time. The more this number, the less time it will take for the failure recovery process to complete. However, sometimes you need the slaves to still serve the old data for the system to continue operation. So you might want the recovery process to configure slaves one by one.

There are other configurations options that you can have a look at in [Redis documentation](http://redis.io/topics/sentinel). Please note that you still can use the command SENTINEL SET to change Redis Sentinel configuration at run time without restart. 


Important commands that you can use to interact with Redis Sentinel are listed below:

* Get the state of a master

````
127.0.0.1:5000> sentinel master mymaster
 1) "name"
 2) "mymaster"
 3) "ip"
 4) "127.0.0.1"
 5) "port"
 6) "6379"
 7) "runid"
 8) "953ae6a589449c13ddefaee3538d356d287f509b"
 9) "flags"
....
````

Get information about all slaves under a certain master using below command:

````
SENTINEL slaves mymaster
````

* Get the address of a master


````
127.0.0.1:5000> SENTINEL get-master-addr-by-name mymaster
1) "127.0.0.1"
2) "6379"
````


There are many other interesting sentinel commands that are listed in [Redis documentation](http://redis.io/topics/sentinel).

