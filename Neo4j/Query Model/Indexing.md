

### [Neo4j](../Neo4j.md) > [Query Model](Query Model.md) > Indexing
___


Neo4j supports the creation of indexes on any property of a node if the node is already tagged to a label. Indexes will be managed automatically by Neo4j and be kept always up to date whenever graph data is updated. Once the index is created, Neo4j will start using the index to retrieve data efficiently whenever you run a query on it. As you might already know, index comes always with a space cost, so create an index only when you need it.

For example, we will create an index on the product name property of all the nodes having the ":Product" label :

````
CREATE INDEX ON :Product(name)
````

Then you can easily run fast queries against it as shown below:

````
MATCH (p:Product { name: 'Dell Laptop' })
RETURN p
````

or by using WHERE clause:

````
MATCH (p:Product)
WHERE p.name = 'Dell Laptop'
RETURN p
````

````
MATCH (p:Product)
WHERE p.name > 'L'
RETURN p
````

````
MATCH (p:Product)
WHERE p.name STARTS WITH 'La'
RETURN p
````

````
MATCH (p:Product)
WHERE HAS (p.name)
RETURN p
````


````
MATCH (p:Product)
WHERE p.name IN  ['Dell Laptop', 'T-Shirt']
RETURN person
````



Finally to drop the index, you can run the below:

````
DROP INDEX ON :Product(name)
````


With Neo4j 2.3, there is support only for automatic indexes which will be created on the properties of a node that is attached to a label and can be used for exact lookups of nodes based on their property values. Other types of indexes such as full-text, spatial, and range indexes are still not addressed but are planned to be targeted in the future roadmap.  However Neo4j still support legacy indexes, but because the use of legacy indexes isn't favoured by Neo4j, we aren't going to cover them here. If you need any information about using legacy indexes, you can have a look at [Neo4j documentations](http://neo4j.com/docs/stable/indexing.html).


If you need to create a composite index to ensure the uniqueness on two or more properties at the same time, then you can create one normal index and use MERGE clause when creating the data as shown below:


````
CREATE CONSTRAINT ON (c:Customer) assert a.composite_id IS UNIQUE;

MERGE (c:Customer {composite_id: [{firstName},{secondName},{age}]}) ON CREATE SET c.firstName = {firstName}, c.secondName={secondName}, c.age = {age};
````
