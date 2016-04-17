

### [Neo4j](../Neo4j.md) > [Administration and Maintenance](Administration and Maintenance.md) > Scalability
___

In this section, I will discuss how Neo4j support scalability in terms of throughput and Capacity.


##### Capacity 


Neo4j has some upper bound limit for the graph size and can support single graphs having tens of billions of nodes, relationships and properties. The current Neo4j version supports up to around 34 billions of nodes and relationships and around 274 billions of properties. This is quite enough for large graphs of Facebook similar network graph size.##### Throughput
As explained in the [availability section](Availability.md), Neo4j supports HA master-slave clusters that can linearly scale reads where slaves can share the read load.  As for the write load, only the master instance in the cluster can handle it. Other slave instances can still receive the write requests from clients but then these requests will be forwarded to the master node. This means that Neo4j doesn't scale the write load very well and in case of exceptionally high write loads, only vertical scaling of the master instance is possible. Although it is possible to build some sharding logic in the client application to distribute the data across number of servers, however the sharding logic still not natively supported by Neo4j. This is because sharding the graph is a near impossible or NP hard problem. In general, sharding the data in the client side depends on the graph structure. If the graph has clear boundaries, then it can be sharded easily otherwise it can be really difficult. The future goal of Neo4j is to support sharding across multiple machines without the application-level intervention, however this might take time.