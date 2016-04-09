[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Administration and Maintenance > Upgrading


Redis doesn't support online upgrades which means that the server must restart and the clients can't connect to it during the upgrade window. However there are some ways that you can use to support online upgrade. One way is to start a slave server and direct all the clients to it while you do an upgrade to the master. After the upgrade is finished in the master, we can stop the slave server and then redirect the clients requests again to the master server. An example is shown below:

First we assume that the master server runs on the default port which 6379. Then we start another slave server with the below command:

````
SLAVEOF localhost 6379
````

Now the new server is a slave of the master server that we want to upgrade. This will also trigger the BGSAVE command in the master server which will create a snapshot of the dataset and transfer it to the slave server. Now since this new slave server is running fine, we can redirect our client to this server and do the upgrade in the master server. After the master server is upgraded and configured correctly, we can then redirect the client to it and stop the slave server.