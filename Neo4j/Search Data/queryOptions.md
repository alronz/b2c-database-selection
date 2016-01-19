#### [back](search_data_main.md)


Neo4j supports many query functions that can be used to offer aggregations functions such as min, max, avg, count, DISTINCT, sum, stdev, stdevp and collect. Most of these functions works exactly in the same way in SQL such as min, max, sum, avg and count. These basic function are used with the RETURN clause to return aggregated results as shown below:

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