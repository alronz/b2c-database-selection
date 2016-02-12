#### [back](admin_main.md)


I will explain in this section briefly the general steps that you need to follow to upgrade your Cassandra to a new release. The steps below needs to be executed in each of the nodes.


1-First of all, you need to read and to familiarize your self with the new changes and features of the new release. 

2- Run the below command to rewrite the SSTables to make sure that all of them are running the current version of Cassandra:

````
nodetool upgradesstables
````


3- Run the below command to flush all the data that are currently in your MemTables to be written to the SSTables:

````
nodetool drain 
````

4- Stop your node as shown below:

````
sudo service cassandra stop
````

5- Take a copy of your current configuration file to be used as a backup because this file might be overwritten with default values of the new Cassandra version.

6- Install the new version.

7-  Configure the new version based on your old configurations and configure any new configurations for any new features.

8- Start the node again using the below command:

````
sudo service cassandra start 
````

9- Upgrade the SSTable as done in step 2 so that the tables will be rewritten to work with the new version :


````
nodetool upgradesstables
````

10-  Finally, check the logs for any errors, warning or exceptions.
