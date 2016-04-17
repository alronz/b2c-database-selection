


### [Neo4j](../Neo4j.md) > [Administration and Maintenance](Administration and Maintenance.md) > Upgrading
___


Neo4j supports automatic upgrade for upgrades between patch releases and within the same minor version. However, upgrading a major release is manual. In this section, I will show how to do manual and automatic upgrade.


##### Automatic Upgrade

Follow the steps below:

1- Shut down the older Neo4j version.
2- Install the new version and configure it to use the same old data store directory.
3- Take a copy of the database.
4- Start Neo4j.


##### Manual Upgrade


Follow the steps below:

1- Shut down the older Neo4j version.
2- Install the new version and configure it to use the same old data store directory.
3- Configure the configuration parameter allow_store_upgrade=true 
4- Start Neo4j.
5- Configure the configuration parameter allow_store_upgrade=false 

