
### [Neo4j](../Neo4j.md) > [Query Model](Query Model.md) > Aggregation and Filtering
___


Filtering, grouping and aggregating in Neo4j is very easy, much similar to relational database using SQL-similar syntax. In the below sections I will explain how filtering, and grouping is supported using Neo4j.



##### Aggregation

Grouping in Neo4j is handled by using node labels as already explained. A node can have one or more labels which can be used later to query only a certain group of nodes in the graph which results in faster queries. Relationships also can have a type to be also grouped later on queries. 

Neo4j supports many query functions that can be used to offer aggregations functions such as min, max, avg, count, DISTINCT, sum, stdev, stdevp and collect. Most of these functions works exactly in the same way in SQL such as min, max, sum, avg and count. The aggregation is done in the RETURN clause and the grouping key(s) will be automatically figured out by the RETURN clause and doesn't need to be specified manually. These basic function are used with the RETURN clause to return aggregated results as shown below:

````
MATCH (p:Product)
RETURN min(p.price), max(p.size) , count(*), sum(p.price), avg(p.price)
````


DISTINCT is used to remove any duplicates from the returned results. This is usually used to return the count of unique nodes based on a certain node property as shown below:

````
MATCH (p:Product)
RETURN count(DISTINCT b.type)
````


stdev and stdevp are used to calculate the standard deviation for a given value over the aggregate. Stdev uses a two-pass method with N-1 as denominator and used mainly for unbiased estimate. In the other hand, stdevp uses also two-pass method but with N as denominator and used mainly to calculate standard deviation for the entire population. Example is shown below:

````
MATCH (p:Product)
WHERE p.name IN ['A', 'B', 'C']
RETURN stdevp(p.property)
````

Finally the collect function is used to collect all the values and store them in a list as shown below:


````
MATCH (p:Product {type: "expensive"})
RETURN collect(n.name)
````

In the above example, we are collecting all product names that are expensive in a list, so we get the results like ['A','B','C'].



##### Filtering

Filtering the data in Noe4j is done by providing a specific match pattern that would be the input for a Cypher MATCH clause. The pattern can handle very complex queries and should describe what we are looking for in a human readable style. We can specify a pattern that involves any number of nodes and relationships and we can go as deep as we want in the graph. We can also do the matching in multiple stages by either using more than one MATCH clause or by combining results from different MATCH clause using the WITH clause. Neo4j supports very flexible filtering functionalities that can be used to answer extensively complex queries. For more details on how to use the MATCH clause, please have a look at the previous section explaining [Cypher query language](../Basics/Query Language.md). The results of the MATCH clause can be then filtered out easily using the WHERE clause.
