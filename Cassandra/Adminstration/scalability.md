#### [back](admin_main.md)


In this section, I will discuss how Neo4j support scaling in terms of throughput and Capacity.


##### Capacity 


Neo4j has some upper bound limit for the graph size and can support a single graphs having tens of billions of nodes,relationships and properties. The current Neo4j version supports up to around 34 billions of nodes and relationships and around 274 billions of properties. This is quite enough for large graphs of Facebook similar network graph size.##### Throughput
As explained in the [availability section](availability.md), Neo4j supports HA master-slave clusters which can be used to horizontally scale reads and writes where slaves can also share the read and write load.  However if we exceed the capacity of one cluster, it is possible to build some sharding logic in the application by using sharding keys and distribute the data across clusters. The sharding logic isn't supported internally by Neo4j and should be implemented in the application level. How sharding a graph can be done, depends mainly on the shape of the graph. If the graph has clear boundaries, then it can be sharded easily and distributes across different clusters. The future goal of Neo4j is to support sharding across multiple machines without the application-level intervention, however this is a known NP hard problem. 