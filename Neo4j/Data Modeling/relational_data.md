#### [back](data_modeling_main.md)

In Neo4j all relationships regardless of whether they are one-to-one, one-to-many or many-to-many are represented in the same way through one relationship. Although relationships in Neo4j are directed but they can be traversed in both direction which means that you just need to create one relationship between different nodes regardless of the direction. It makes sense to create more than one relationship between two different nodes if they are of a different type or meaning. Because then you can use these different relationships to answer different queries. For instance, if you want to have a many-to-many relationship between products and their categories, then you can just create one relationship either a relationship called PART_OF which started from the nodes with label :Product and ends with nodes with label :Category. Or you create a relationship called HAVING which starts from the nodes with label :Category and ends with nodes with label :Product. However you need to create only one relationship and not both because they have the same meaning and will answer the same queries. Assuming we choose to create the relationship called PART_OF which starts from the :Product nodes and ends at the :Category nodes. Then we can answer both below queries using this relationship:

"What are all the products under category A ?"
"What are the categories that contains product A ?"


To answer the first query, we can run a Cypher query as shown below:

````
MATCH (p:Product)-[r:PART_OF]->(c:Category {name: "A"})
RETURN  p;
````

And to answer the second query, we can run a Cypher query as shown below:

````
MATCH (p:Product {name: "A"})-[r:PART_OF]->(c:Category)
RETURN  c;
````


As seen above, the same relationship can be used to traverse in both direction.

To represent one-to-many or one-to-one relationships, there is no special care. We need just one relationship between the nodes as we did with the many-to-many relationship above and then we can easily run Cypher queries as we want.


 