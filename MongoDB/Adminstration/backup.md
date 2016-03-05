#### [back](../admin_main.md)


In any production environment, we should have a backup strategy to restore our data back in case of failures. MongoDB provides a tool called mongodump that can read data from the database and creates a backup BSON files. To restore the created backup files, you can use the mongorestore tool to populate the MongoDB database with the backup BSON files.


After restoring the data using mongorestore, the mongod instance must rebuild all indexes. Mongodump tool can impact the mongod instance performance, so it is usually recommended to run this tool against a secondary member in a replica set. 

If you are using a replica set configuration, it is also possible to take a point in time backup using mongodump with the --oplog option. This means that mongodump will still be able to capture the changes to the data if the application modifies the data while backing up. To restore a point in time backup, you can use the mongorestore with the --oplogReplay option.


You can take a backup for the whole server or for a specific collection. To create a backup on a certain server, you can run the below command:

````
mongodump --host mongodb.example.net --port 27017
````

The above command will create the backup database with name dump/ in the current directory. 

To take a backup for a particular collection in a database, you can use the below command:

````
mongodump --collection myCollection --db test
````


To restore a specific backup, you can run the below command:

````
mongorestore --port <port number> <path to the backup>
````
