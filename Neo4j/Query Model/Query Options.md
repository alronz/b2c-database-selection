

### [Neo4j](../Neo4j.md) > [Query Model](Query Model.md) > Query Options
___


Neo4j supports most query types by using the MATCH and WHERE clauses together. To supports parameterised or range queries by using the WHERE clause as shown below:


parameterised queries :

````
MATCH (p:Product)
WHERE p.name = 'T-Shirt'
RETURN p
````

Range queries:

````
MATCH (p:Product)
WHERE p.price < 2000 AND p.price > 200
RETURN p
````


For more details about the WHERE and MATCH clause, please read the previous section about [Cypher query language](../Basics/Query Language.md).

