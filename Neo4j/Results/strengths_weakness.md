In this section I will talk about the strengths and weaknesses of Neo4j and when using it makes sense.



##### Strengths

Like any graph database, Neo4j has the below advantages:

###### Performance


Graph databases as well as Neo4j provides much better performance when it comes to querying deeply connected data that has many relationships expressed with complex joins. In relational databases, join-intensive query performance deteriorates when the dataset gets bigger. However when using graph databases, the performance stays relatively constant even with very large datasets.  This because in the graph data model, the query will check only the part of the graph that will be traversed by the query and not the whole graph.  


###### Flexibility


The graph data model is more natural. It has no impedance mismatch and is whiteboard friendly. This means you can use the language of node, relationships and properties to describe the application domain instead of using complex models such as UML. Then this graph model is directly mapped and implemented in the database. This friendly data model used in the graph databases allows developers to be more productive and reduce project risk. Since the graph model is flexible, you can start with small model and improve it in the future easily by adding more nodes and relationships with fewer migration and maintenance overhead.  


###### Powerful Query Model


The graph query model is so intuitive and makes it very suitable for applications with object oriented, semi-structured and network like data. The graph model is also very natural to express graph related problems such as the path finding problems.  Using this query model, you can write complex high performance traversals that can be beneficial in many use cases.  
  

In addition to the above advantages of the graph databases, Neo4j also provides the below advantages.

######  Easy to Learn Query Language


Neo4j provides a powerful traversal framework using an easy to learn query language called Cypher. Cypher is a declarative query language designed to be an efficient and human readable language.


###### ACID Compliant

Neo4j is compliant with the ACID properties (Atomicity, Consistency, Isolation, Durability) and provides full transaction support


##### Weaknesses

Neo4j has the below main weaknesses:

###### Scalability

Neo4j supports HA master-slave clusters that can linearly scale reads where slaves can share the read load.  As for the write load, only the master instance in the cluster can handle it. Other slave instances can still receive the write requests from clients but then these requests will be forwarded to the master node. Therefore, writing to the master instance is faster than writing to a slave instance. This means that Neo4j doesn't scale writes very well and in case of exceptionally high write loads, only vertical scaling of the master instance is possible. Although it is possible to implement some sharding logic in the client application to distribute the data across number of servers, however the sharding logic still not natively supported by Neo4j. This is because sharding the graph is a near impossible or NP hard mathematical problem. In general, sharding the data in the client side depends on the graph structure. If the graph has clear boundaries, then it can be sharded easily otherwise it can be really difficult. Additionally it is very difficult to shard a densely connected graph.


###### Storage

Neo4j has some upper bound limit for the graph size and can support single graphs having tens of billions of nodes, relationships and properties. The current Neo4j version supports up to around 34 billions of nodes and relationships and around 274 billions of properties. This is quite enough for large graphs of Facebook similar network graph size. These storage constraints doesn't pose any limitations in practice since only big businesses such as google can push these limits and these limits were set for storage optimisations and can be increased in future versions.

###### No date data type support support

Neo4j doesn't have internal support for date data type but this can be overcome using different methods such as storing the Epoch linux long values instead.  


##### Summary


Neo4j is suitable for applications where you have very large connected data and you want to run complex join-intensive queries and still get high performance. It can be used for example to run live complex queries (reports) in production data since they will be faster. In addition, Neo4j is suitable if you have a data that will be more naturally represented using the graph model such as network like, semi-structured or highly connected data. However, if you have simple data model that don't require a lot of joining or aggregation, then you might not get any performance improvements if you use Neo4j or the performance advantage will be very small if not worse. Additionally, Neo4j has scalability weaknesses related to scaling writes, hence if your application is expected to have very large write throughputs, then Neo4j is not for you. 