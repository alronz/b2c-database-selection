#### [back](search_data_main.md)


To sort the results returned by a query, Neo4j supports a SQL-like clause called ORDER BY which is used at the end of your query after the RETURN or WITH clauses. The ORDER BY clause will simply order the returned results based on a property field projected by the RETURN or WITH clauses. This means that you can only order what has been returned previously.

For example if your query returns only product name, size and price. The you can sort your results by either product name, size or price as shown below:


````
MATCH (p:Product)
RETURN p.name, p.size , p.price
ORDER BY p.name DESC
```` 

You can specify the sort order by using either DESC or ASC as shown above. You can't use any aggregation expressions with ORDER BY since it is meant only for sorting and not changing the results. Finally if you sort any results using ORDER BY, then any NULL values will come always at the end of the results.