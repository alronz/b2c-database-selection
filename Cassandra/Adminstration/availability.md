#### [back](admin_main.md)


Cassandra is a highly available distributed system. This is because You can easily replicate your data across multiple nodes or even data centres that spans multiple locations. Replicating the data prevents downtime in case the node or the data centre experiences a catastrophic event or an unexpected failure since the data can be still served from the replica nodes. 

Cassandra is a decentralised system that doesn't have a single point of failure since it is a masterless system and all nodes are equally important. When we create the Keyspace, we need to decide about the replication factor and the replication strategy. The replication factor defines how many copies or replicas of our data that we want to keep. And the replication strategy defines where we are planning to replciate the data. Cassandra nodes uses a gossip protocol to know the state of all other nodes. Based on the state of the nodes, each node will decide where to forward the received read/write requests to ensure availability.  Replicating the data in more than one node will ensure reliability and fault tolerance. Once a node is down, then all read and write requests will be rerouted to the replica nodes.


![image](https://www.datastax.com/wp-content/uploads/2014/10/Cassandra_Request.jpg)


The replication strategy used in each node to determine the physical location of the nodes that will be used as a replica. Replication strategy uses the Snitch which is a protocol used to determine which nodes are performing well and which aren't. Based on the snitch information, the replication strategy will replicate the data to the best performing nodes. There are different types of replication strategies that can be configured in Cassandra. For example, the SimpleStrategy is used if we have single data centre or only an individual node. This is the default strategy used and it will simply choose the replica based on the partitioner. In the SimpleStrategy, the first replica will be the first node selected by the partitioner and then the second node is the next node that was added clock-wisely to the ring and so forth.  However, it is encouraged to use the NetworkTopologyStrategy replication strategy instead of the simple strategy especially if your deployment span multiple data centres. This strategy will define how many replicas you require to have in each data centre. Then the NetworkTopologyStrategy will choose the replicas by walking the ring clock-wisely until reaching the initial node in another rack. Additionally, the NetworkTopologyStrategy will make sure to choose replicas in different racks due to the fact that nodes in the same rack tend to fail together because of power or network issues.


