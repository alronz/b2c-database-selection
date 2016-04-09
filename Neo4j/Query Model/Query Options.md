[Home](../../index.md)

[Neo4j Content](../Neo4j.md)
___

# Neo4j > Query Model > Query Options



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


For more details about the WHERE and MATCH clause, please read the previous section about [Cypher query language](../Basic Features/commands.md).

