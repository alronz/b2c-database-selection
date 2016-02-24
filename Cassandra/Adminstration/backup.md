#### [back](admin_main.md)


Cassandra supports taking backups through snapshots and by enabling the incremental backup feature. The snapshot is taken for the SSTable files in a node either for a single keyspace or for all keyspaces or even for the whole cluster. Generally, a system-wide snapshot is taken then the incremental backup feature is enabled in each node to backup only the delta data that has been changed since the last snapshot. 


To take a snapshot on a node, we use the nodetool snapshot command as shown below:


````
nodetool -h localhost -p 7199 snapshot mykeyspace
```` 

Then the snapshot will be created under the snapshot folder in your main data directory. 

Cassandra doesn't delete the old snapshot files automatically, so you need to delete them manually if you no longer need them using the following command:

````
nodetool -h localhost -p 7199 clearsnapshot
```` 

The above command will delete all the existing snapshots, so generally you need to run this command before you start taking new snapshot to save space by deleting old snapshot files. 

To enable the incremental backup, you need to set the incremental_backups parameter in the configuration file to true.

