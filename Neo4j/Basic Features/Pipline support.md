#### [back](basic_features_main.md)


In case you have a huge data set that you want to insert into Neo4j at once to migrate from another datasource, Cypher language supports loading data as parameters. These parameters can be either scalar values, maps, lists or lists of maps. The UNWIND clause can be of a great help to expand the imported data and insert it one by one to create the graph structure. You can also import easily a JSON data pulled from some API as seen below:


The products JSON data:

````
{
  "products" : [ {
    "name" : "T-Shirt",
    "size" : 20,
    "price": 200 
    },
    {
    "name" : "Laptop",
    "size" : 15,
    "price": 1200 
  } ]
}
````

Then imported as:

````
UNWIND {products} as product
MERGE (p:Product {name:product.name}) ON CREATE SET p.created = timestamp()
````


Another option to import huge set of data to Neo4j is by importing the data from csv files.
Cypher supports the LOAD CSV clause that will parses a local or remote file into sequence of rows, then you can use Cypher clauses and operations to create the desired nodes and relationships. Usually it would be better if you load the nodes and relationships in separate CSV files. The example shows how to load a CSV file contains a set of products with the format below:

````
id,name,size,price
1,T-Shirt,20,200
2,Laptop,15,1200
3,Iphone,5,600
4,Samsung 5s,4,400
````

Then you load the data as seen below:


````
LOAD CSV WITH HEADERS FROM "http://{url}/products.csv" AS line
MERGE (p:Product { id:line.id })
ON CREATE SET p.name=line.name,p.size=line.size,p.price=line.price;
````

Then to create relationships, we can load the orders and create the relationship between the order and product while loading as seen below:

First load the orders:

````
LOAD CSV WITH HEADERS FROM "http://{url}/orders.csv" AS line
MERGE (o:Order { id:line.id })
ON CREATE SET o.status=line.status,o.total=line.total;
````

Then load the OrdersProducts table

````
LOAD CSV WITH HEADERS FROM "http://{url}/OrdersProducts.csv" AS line
MATCH (p:Product { id:line.productId })
MATCH (o:Order { id:line.orderId })
CREATE (o)-[:Contains]->(p);
````