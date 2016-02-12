#### [back](admin_main.md)


Cassandra supports taking backups through snapshots and by enabling the incremental backup feature. The snapshot is taken for all the on-disk data or the SSTable files in a node. It can be taken for a single keyspace or for all keyspaces. It is also possible to take a snapshot for the whole cluster. In general, first we take a system-wide snapshot then we enable the incremental backup feature in each node to backup the data that will be changed after taking that snapshot. 


To take a snapshot on a node, we sue the nodetool snapshot command as shown below:


````
nodetool -h localhost -p 7199 snapshot mykeyspace
```` 

Then the snapshot will be created under the snapshot folder in your main data directory. 

Cassandra doesn't delete the old snapshot files automatically and you need to delete them manually if you no longer need them using the following command:

````
nodetool -h localhost -p 7199 clearsnapshot
```` 

The above command will delete all the snapshots, so generally you need to run this command before you start taking new snapshot to save space by deleting old snapshot files. 

To enable the incremental backup, you need to set the incremental_backups parameter in the configuration file to true.

