

### [Cassandra](../Cassandra.md) > [Administration and Maintenance](Administration and Maintenance.md) > Scalability

___

Cassandra uses a peer-to-peer distribution model, such that all nodes in a cluster have an equal importance and there is no concept for a master or a salve. This makes Cassandra design very scalable and highly available. This peer-to-peer design Improves the overall scalability since all the nodes in a cluster can serve read or write requests and there is no single point of failure.  Because all nodes can serve read and write requests, scaling the system is as simple as adding new node to the cluster. This makes Cassandra elastically scalable since you can easily scale up or down by just adding or removing nodes from the system.  Adding nodes to the cluster is also largely automated and has minimal configuration.  

The data in Cassandra is partitioned across the different system nodes by using the Partitioner that uses a hashing function to create a token based on the partition key to decide where to put the data. Then when a node receives a read or write request from an application client, it will check with the Partitioner to find in which node the requested data is stored. Then it will act as a coordinator to forward the request to the right node. Once the read or write request is a successful based on the configured consistency level, it will forward back the results to the client. So read and write requests are performed in a similar way and can be fulfilled by any node connected to the system. Horizontal scaling the system is then as simple as adding or removing nodes, data centres or even clusters.

![image](http://www.rapidvaluesolutions.com/wp-content/uploads/2015/07/Image-12.png)



![image](http://www.rapidvaluesolutions.com/wp-content/uploads/2015/07/Image-1.png)


After the virtual nodes has been introduced in Cassandra, adding and removing nodes is easily done using the nodetool commands. The node will join the existing cluster and will automatically will be responsible for an even portion of the data from the other nodes in the cluster. Removing the nodes is also easily done using the nodetool command (nodetool decommission) which will remove the node from the cluster and assign its data evenly to the other nodes in the cluster.
