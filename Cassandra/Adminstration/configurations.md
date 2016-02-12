#### [back](admin_main.md)


Cassandra uses a yaml configuration file called cassandra.yaml to store all configuration related parameters. The configuration file can be found under the install_location/conf folder. Cassandra doesn't support changing the configurations on the fly and you will need to restart the node for the new configurations to take effect. 

Cassandra have many configuration parameters that can be used to tune the different options and capabilities of Cassandra. Below I will show few common parameters as an example:


````
cluster_name
This configuration parameter is used to allow the node to join a specific cluster.  The default cluster name is "Test Cluster"


commitlog_directory
This is where you set where the commit log file is stored.

data_file_directories
This is where you set where the SSTables files are stored.
````







