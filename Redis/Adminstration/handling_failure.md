#### [back](admin_main.md)

If Redis server confronted with some system failures, there are tools available to recover as long as the snapshotting or the append-only file persistence methods are used. Redis provides two commands that can be used to verify the status of a snapshot or the AOF. The commands are redis-check-aof and redis-check-dump. An example is shown below:

````
$ redis-check-aofUsage: redis-check-aof [--fix] <file.aof>$ redis-check-dumpUsage: redis-check-dump <dump.rdb>
````

Using --fix argument will fix the AOF by scanning the file for an incomplete or incorrect command. However unfortunately Redis doesn't support a way to fix the snapshot if it is corrupted. 


#### Handling failure in master-slave model using Redis cluster

Using Redis cluster, recovering a failed master is possible and easy only if replicas or slaves exist for each master. For instance if you have three master nodes in your cluster A,B, and C and no replicas, then if node A fails the cluster isn't going to be able to continue since there is no way to serve hash slots that were in the failed server. However if we have six nodes where each master node is have a slave node as a Replica, then the cluster can easily recover in case of a failure in a master node. So in the previous example if node A fails, the cluster will promote its replica and the cluster will continue to operate correctly. Redis Sentinel can be also used to automatically handle failure if master goes down. Redis Sentinel will be discussed on the [availability section](link to the availability section). 