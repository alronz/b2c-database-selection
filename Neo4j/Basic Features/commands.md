#### [back](basic_features_main.md)


Neo4j uses a declarative query language called Cypher to interact with the stored data. Cycler is a powerful relatively simple language which is designed to be efficient and human readable. It is inspired by SQL and use similar clauses such as WHERE and ORDER BY and its pattern matching expressions are inspired by SPARQL. In the following sections, I will talk in more details about Cypher main concepts, Cypher general clauses, and how to read and write data using Cypher.


##### Cypher Concepts

Cypher uses a SQL-Like syntax. Below I will explain briefly the node and relationship syntax:

````
()
(t-shirt)
(:Product)
(t-shirt:Product)
(t-shirt:Product {name: "t-shirt"})
(t-shirt:Product {name: "t-shirt", size: 20})
````

Nodes are identified by using (), above I have shown 6 ways of representing a node. We can either use the general form () which means a general nameless node. Also we can represent a node with some identifier (t-shirt), identifier and a label (t-shirt:Product), identifier, a label and some property (t-shirt:Product {name: "t-shirt"}) or more than one property (t-shirt:Product {name: "t-shirt", size: 20}).

The relationship syntax is very similar as shown below:


````
-->
-[bought_by_John]->
-[:Bought_By]->
-[bought_by_John:Bought_By]->
-[bought_by_John:Bought_By {date: "01.01.2016"}]->
````

As seen above , we can represent a general directed relationship using "-->" or "<--" or you can represent a relationship either by using an identifier "-[bought_by_John]->", a relationship type only "-[:Bought_By]->", an identifier and a relationship type  "-[bought_by_John:Bought_By]->", or by using an identifier, relationship type and some property "-[bought_by_John:Bought_By {date: "01.01.2016"}]->".


Another important concept is the pattern concept. Nodes are connected with each other using relationships forming different patterns that are used by Neo4j to answer different queries. In general, nodes and relationships alone encode little information, however patterns of nodes and relations can encode very complex queries.  Cypher is based mainly on patterns. Using patterns, Cypher can find different structures on the graph of the stored data and answer queries efficiently. An example of a pattern is shown below:

````
(:Product) -[:Bought_BY]-> (:Customer) -[:Lives_In]-> (:Country)
````

In the above pattern, we can know all products that are bought by customers living in a specific country.

The last concept is the concept of clauses. Cypher statements contains multiple clauses to create, match patterns, filter, paginate, or sort results. In the below sections, I will talk about Cypher general clauses then I will talk about Cypher clauses used for reading and writing data. 


##### General Clauses

In this section I will talk about general clauses such as RETURN, ORDER BY, LIMIT, SKIP, WITH, UNWIND and UNION.

RETURN clause is used to define what to return in a query. It acts like the SELECT clause in SQL. The return results specified by RETURN can be either a node, relationship or properties. Example is shown below:


````
MATCH (product:Product)RETURN product.price
````

We can use expressions also with the return clause as seen below:

````
MATCH (product:Product)RETURN product.price > 20 
````
The above query will return true or false based on the price of the product.


You can use also the DISTINCT keyword to remove duplicate from results as seen in the example below:

````
MATCH (product:Product)RETURN DISTINCT product.name 
````

ORDER BY clause is used in a similar way like it is used by SQL to sort the result based on a certain property as seen in the example below:

````
MATCH (product:Product)RETURN product.name
ORDER BY product.price
````


LIMIT and SKIP is used for paginate results as show below:

First page:

````
MATCH (product:Product)RETURN product.name
ORDER BY product.price
LIMIT 3
SKIP 0
````

Second Page:

````
MATCH (product:Product)RETURN product.name
ORDER BY product.price
LIMIT 3
SKIP 3
````



WITH is used to pipe the results into multiple stages as seen in the query below:

````
MATCH (product:Product)WITH product.name,product.price
WHERE product.price > 20
RETURN product.name
````

UNWIND is used if you want to expand a collection into a sequence of rows, example is show below:

````
UNWIND[1,2,3] AS x
````

UNION is used in a similar way as used in SQL to combine the results from two or more queries as shown below:

````
MATCH (n:Customer) RETURN n.customerName AS nameUNION ALL MATCH (n:Supplier)RETURN n.supplierName AS name
````

##### Reading Data

The main Cypher clauses used for reading data are MATCH and WHERE. MATCH clause is used to search the graph structure to find a certain pattern given by the query. Usually Match clause is used with WHERE to limit the search for the required pattern. An example to show how to use MATCH with WHERE clauses is to find the orders generated from customers of a particular country, the query will be written as shown below:


````
MATCH (o:Order)-[:Made_BY]-> (:Customer) -[:Lives_In]-> (c:Country)
where o.status = 'Paid'RETURN c.name, count(o.orderID) AS orderCount
ORDER BY orderCount
````

The WHERE clause can be used to find nodes with particular label, relationships with certain relationship type, or to search for nodes with specific property value. Where supports also the use of boolean operators such as OR,AND,XOR and etc.  Similarly it can be used with string matching patterns such as START WITH, ENDS WITH and CONTAINS. Regular expressions is also supported as will be explained in [later section](to the regular expression section).

##### Writing Data

In this section, I will explain how to use Cypher clauses to create, update and delete data stored in Neo4j.

To create a node or a relationship, the CREATE clause can be used. Example shown below to create a product node:


````
CREATE (product1:Product { name : 'T-Shirt', size : '20', price: '200' })
````

To Create a relationship, you can use the below command:

````
CREATE (product1:Product)-[r:Bought_BY { date : '01.01.2016'}]->(customer1:Customer))
````

If you want to create a pattern but to ensure not to create a duplicate, you can use MERGE instead of CREATE. MERGE will first check if the pattern already exists, and if so it won't create a new one. Otherwise it will create the pattern. So it is like a combination of MATCH and CREATE. MERGE can be used also with ON CREATE and ON MATCH options. For example when creating a new order, you can use either ON CREATE or ON MATCH to set the creation timestamp. If you use ON MATCH, it will take the time when a match found. But if you use ON CREATE, it will take the creation timestamp which is what we want.


````
MERGE (order1:Order)-[r:Contains]-> (product1:Product)ON CREATE SET order1.created = timestamp()ON MATCH SET product1.lastseen = timestamp()RETURN product1.name, order1.created, product1.lastseen
````


To update the node label or its properties or the relationship type or its properties, you can use SET clause. An example is given below:

````
MATCH (n { name: 'T-Shirt' })SET n.price = '300'RETURN n````


To remove a property of a node or a relationship, the REMOVE clause can be used.

````
MATCH (n { name: 'T-Shirt' })REMOVE n.extraFieldRETURN n````
To delete a relationship or a node, the DELETE clause can be used as shown below:
````MATCH (product1:Product)DELETE product1
````

Cypher also supports FOREACH clause to be used to update multiple elements along the returned path. For instance, if you want to update all products that are having orders you can use the below query:

````
MATCH p =(p:Product)-[*]->(o:Order)FOREACH (n IN nodes(p)| SET o.status = 'Archived' )
````




