#### [back](getting_started_main.md)

In this section, I will explain briefly the main concepts used in Neo4j.

##### Nodes

The nodes are the fundamental unit used in Neo4j to represent objects or entities that needs to be stored in the database. Each node can store the data related to the entity it represents using map-like properties. For example in a B2C application, each product can be stored as a node and then each node will store the product related information as set of key-value properties as shown below:

![image](https://s3.amazonaws.com/3arta/Untitled+Diagram.png)


##### Relationships

The connection between the entities are represented as relationships. A node can have one or more relationships with one or more nodes. The relationships are directed which means they must have a start and an end node. These relationships can be used later by Neo4j to find related data or to perform complex queries. The relationships can also store one or more properties to hold relationship related information. An example for a relationship between products and categories is shown below: 

![image](https://s3.amazonaws.com/3arta/relationship.png)


As shown above, the product node has an outgoing relationship while the category node has an incoming relationship. It is also possible that the node has a relationship to itself. Relationships are traversed in both directions which means that there is no need to duplicate a relationship for the opposite direction.

##### Properties

As explained, both nodes and relationships can use one or more properties to store some information about the node or the relationship. Properties are key-value attributes with values having a variety of types such as numeric, strings, booleans or even a collection of these types.  However empty or null values can't be stored. 

##### Labels and Relationship Types

A useful feature supported by Neo4j is the ability to assign some labels to nodes which can be used later to easily identify a set of related nodes. For example, it would be a good idea to assign a label to all nodes storing products information and differentiate them from the other nodes that store category information as seen below:

![image](https://s3.amazonaws.com/3arta/label1.png)

Now you can run faster and more efficient queries on the nodes having the "Product" label since the queries will be executed only on the part of the graph having this label.  In the other hand, relationships can have a type to group similar relationships and later make it faster for the database to traverse the graph. In the above example, the relationship between the product and the category has the "IS_PART_OF" type.


##### Traversal


The traversal is the name given for how the database search the graph to find results of your queries.  It means visiting the nodes and following relationships along the way according to rules given in your query.



##### Paths

The path is basically the traversal result. It contains one or more nodes connected with some relationships. 


##### Schema

As most NoSQL database, Neo4j is schema-less. However it supports an optional way of enforcing a schema for your data using constraints. This can be very useful in some production environment and can increase performance or add some modelling benefits. 






