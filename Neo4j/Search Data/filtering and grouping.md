#### [back](search_data_main.md)


Filtering, grouping and aggregating in Neo4j is very easy, much similar to relational database using SQL easy syntax. In the below sections I will explain how filtering, grouping and aggregating is supported using Neo4j.


##### Filtering

Filtering the data in Noe4j is done by providing a specific match pattern that would be the input for a Cypher MATCH clause. The pattern can handle very complex queries and should describe what we are looking for in a human readable style. We can specify a pattern that involves any number of nodes and relationships and we can go as deep as we want in the graph. We can also do the matching in multiple stages by either using more than one MATCH clause or by combining results from different MATCH clause using the WITH clause. Neo4j supports very flexible filtering functionalities that can be used to answer extensively complex queries. For more details on how to use the MATCH clause, please have a look at the previous section explaining [Cypher query language](../Basic Features/commands.md).


##### Grouping

Grouping in Neo4j is handled by using node labels as already explained. A node can have one or more labels which can be used later to query only a certain group of nodes in the graph which results in faster queries. Relationships also can have a type to be also grouped later on queries. 


##### Aggregation

Aggregation in Neo4j is easily supported using Cypher language by using many functions such as min, max, avg, sum, and count which are explained in [previous section](queryOptions.md). The aggregation is done in the RETURN clause and the grouping key(s) will be automatically figured out by the RETURN clause and doesn't need to be specified manually. 
