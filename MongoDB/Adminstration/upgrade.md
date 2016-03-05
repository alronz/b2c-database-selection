#### [back](admin_main.md)

In this section I will show how to upgrade MongoDB version 3.0 to the latest version 3.2.  To upgrade a MongoDB instance, you should start by shutting it down using mongod --shutdown option. After that you can either manually upgrading by downloading the new 3.2 binaries and then just replace them with the old 3.0 binaries or you can use the package managers such as apt, dnf, yum, or brew. For instruction on how to install MongoDB 3.2, please have  a look to the [installation section](../Getting Started/installation.md). 

If you are using more complex deployment such as using sharded clusters or replication. Please have a look to [MongoDB documentations](https://docs.mongodb.org/manual/release-notes/3.2-upgrade/) for more detailed instructions.