#### [back](getting_started_main.md)

In this section, I will explain briefly with examples the main concepts used in Neo4j and most similar graph based databases.


##### Nodes

The nodes are the fundamental unit used by Neo4j to represent objects or entities that you want to store in your database. The nodes can have one or more properties that will be explained in later section. For example, if you have want to store your products in a B2C application, you can store each product as a node and each node can have the product details stored as set of key-value properties as shown below:

![image](https://s3.amazonaws.com/3arta/Untitled+Diagram.png)


##### Relationships

The connection between each node and another is called a relationship in Neo4j. A node can have one or more relationships with one or more nodes. The relationships are directed which means that they must have a start and an end node. These relationships can be used later by Neo4j to find related data and perform complex queries. The relationships can also have one or more properties to store relationship related information. An example below shows a relationship between a product and a category nodes. 

![image](https://s3.amazonaws.com/3arta/relationship.png)


In the above figure, we can say that the product node has an outgoing relationship, while the category node has an incoming relationship. It is also possible that the node has a relationship to itself. Important is that the relationships are traversed in both directions which means that there is no duplicate relationship in the opposite direction.

##### Properties

As seen before, both node and relationship can have one or more properties to store some information about the node or the relationship. Properties are key-value attributes with values having a variety of types such as numeric, strings, booleans or even a collection of these types.  However empty or null values can't be stored. 

##### Labels and Relationship Types

A useful feature supported by Neo4j is the ability to assign some labels to nodes which can be used later to easily identify a set of related nodes. For example, it would be a good idea to assign a label to all nodes storing products information and differentiate them from the other nodes that store categories information as seen below:

![image](https://s3.amazonaws.com/3arta/label1.png)

Now you can run faster and more efficient queries on the nodes having the "Product" label since the queries will be executed only on this part of the graph.  In the other hand, relationships can have a type to group relations with same type and make it faster for the database to traverse  the graph and return query results. In the above example, the relationship between the product and the category has the "IS_PART_OF" type.


##### Traversal


The traversal is the name given for how the database query the graph to find results of your queries.  It means visiting the nodes and following relationships along the way according to rules given in your query.



##### Paths

The path is basically the traversal result. It contains one or more nodes connected with some relationships. 


##### Schema

As most NoSQL database, Neo4j is schema-less. However it supports an optional way of defining a schema for your data which can be very useful in some production environment and can increase performance and provides some modelling benefits. 






