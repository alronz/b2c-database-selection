#### [back](data_modeling_main.md)


Referential integrity isn't enforced in the same way as in relational databases where if one row is deleted , all its related data is also removed. In Neo4j, if we delete a node, all its relationships will be also removed since we can't have a relationship without a start and end node attached to it. However the connected node at the end of the relationship won't be deleted. In other words, data integrity in Neo4j has other which is that there shouldn't be any relationship without start or end node and there shouldn't be any properties that aren't attached to a node or a relationship. 


In addition, Neo4j enforces data integrity through the use of some constraints such as the property unique constraint that ensures the uniqueness of a certain node.
For example if you want to ensure that there will be no node with label :Product with the same productID, then you can create a constraint as shown below:


````
CREATE CONSTRAINT ON (p:Product) ASSERT p.ID IS UNIQUE
````


Another constraint is that you can ensure that all the nodes with specific label should always have a certain property. To create such a constraint, you can run the below:

````
CREATE CONSTRAINT ON (p:Product) ASSERT exists(p.ID)
````

In the above, we create a constraint to ensure that all nodes with label Product should always have an ID property.

For relationships, you can create a constraints only to ensure the existence of a certain property as shown below:



````
CREATE (p:Product)-[r:Part_OF]->(c:Category) ASSERT exists(r.since)
````

In the above example, we are creating a constraint to make sure that there will be no PART_OF relationship between products and categories without specifying since when this product is part of this category. 