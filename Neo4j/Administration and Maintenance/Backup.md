[Home](../../index.md)

[Neo4j Content](../Neo4j.md)
___

# Neo4j > Administration and Maintenance > Backup

Neo4j provides an online backup tool called neo4j-backup that will be enabled by default and will store a local copy of the database. To enable it in case it wasn't not enabled, you can set the configuration parameter online_backup_enabled to true. By default the backup tool will listen to the loopback local host and the port 6362 but you can listen to other database instance by setting the online_backup_server configuration parameter. The backup tool will take an incremental backup after taking the first backup.  For more configuration options related to the backup tool, please check the [documentations](http://neo4j.com/docs/stable/backup-introduction.html).

To perform a manual backup, you can use the below command:

A full backup:

````
$ mkdir /mnt/backup/neo4j-backup
$ ./bin/neo4j-backup -host 192.168.1.34 -to /mnt/backup/neo4j-backup
````

An incremental backup:

````
./bin/neo4j-backup -host 192.168.1.34 -to /mnt/backup/neo4j-backup
````

The backups can then act as fully functional databases in case of a failure. To restore the database in case of failure, you can just replace your database folder with the backed up version. If you are using the master-slave cluster configuration, you need first to shut down all the database instances in the cluster. Then replace the database folder with the backed up database. Finally start a node and issue an update transaction which will propagate the new database to all instances in the cluster.

