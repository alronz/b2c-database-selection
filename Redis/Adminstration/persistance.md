#### [back](admin_main.md)

Redis provides two options to persist data to disk. The first option is called snapshotting or RDB which takes a snapshot of the data that exists at a certain configured moment in time and store it to disk. The second option is called append-only file or AOF which simply works by logging the operations that modifies the data (write operations) into a log file in disk. Processing this log file later if needed will produce the same dataset that was in memory. More details about these two options and how to configure them will be explained in the following sections.

#### Snapshotting or RDB Persistence

Snapshotting is the simplest form of persistence provided by Redis. Using this option, Redis produces snapshots that contains the in memory dataset at a specific point-in-time and when certain conditions are met. For instance, you can configure Redis to produce snapshots each 20 minutes but only if there are already 50 new writes. Which means that in the event of a crash or any other catastrophic incidents, up to 20 minutes of writes can be lost. These snapshots will be stored in disk inside a single .rdb file. 

Snapshotting is an excellent persistence option in terms of performance and can be used for disaster recovery or backups. Redis also use it internally when performing synchronisation between slave and master instances in the replication option for scalability. Unfortunately, Snapshotting doesn't provide good durability guarantees when few minutes data lose isn't acceptable in case of server crashes or any incident between snapshots. 

Configuring Snapshotting is so easy, you just need to specify when to create the snapshots and in which condition. For example, if you want your snapshots to be taken each 100 seconds and in the condition that there are already 50 new writes, you would add the below in the configuration file:

````
save 100 50
````
To save the snapshot manually, you can issue the below command which will be forked and done in the background: 

````
BGSAVE
````
 
#### Append-Only File or AOF Persistence

To have a full durable persistency option, you can use the AOF. This option can be also easily configured to log any operation that modifies the in-memory dataset to disk. You can turn this option on by the adding the below into the configuration file:

````
appendonly yes 
````

After using this option, any write commands received by Redis will be automatically logged into the AOF. After restarting Redis instance it will re-play all the commands in the AOF and recover the original dataset. 

As you might have already guessed it, the log file will get bigger and bigger with time as all write commands are written to it. To solve this problem, Redis supports a process that runs in background without interrupting your work to rewrite the AOF with the shortest possible sequence of commands. You can also start this process manually by calling the below command:

````
BGREWRITEAOF  
````

Depending on your durability requirements, you need to set the fsync options. Redis has three configuration options which determines when to [fsync](http://linux.die.net/man/2/fsync) data to disk as shown below:
 - fsync every when new command is logged to the AOF. This option is used if you want to have a full durability support but it is also a very slow option. 
 - fsync every second. This is the default configuration which will provide a good durability option but with a 1 second data lose risk in case of a disaster.This option is fast enough.
 - Never fsync, using this option will let the operating system handle the fsync to disk which can be very unsafe in term of durability. On the other hand, this option is very fast.

##### Which one to use

Usually the best thing to do is to combine both options to get reliable and durable persistency for your application. This means that you use snapshotting to get a snapshot of your data from time to time which can be used in case of disaster recovery, backups or in case of issues in your AOF. At the same time, you use AOF to get full durability. This is of course depends on your application requirements because in some cases you care only about performance or you can live with some minutes data lose.


#### ACID Support

Here I am going to discuss how Redis the ACID properties:

Atomicity and consistency can be guaranteed by executing a group of commands either using the pipelining support (MULTI/EXEC/WATCH/UNWATCH blocks ) or by using a Lua script.
Isolation is always guaranteed at command level, and for group of commands it can be also guaranteed by MULTI/EXEC block or a Lua script.
Full Durability can be also guaranteed when using AOF with executing fsync with each new command as explained before.
