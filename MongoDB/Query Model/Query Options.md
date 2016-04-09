
[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Query Model > Query Options



MongoDB supports one simple method db.collection.find() to search for documents inside a certain collection. This method accepts two inputs, the first input is the selection criteria for you query and the second input is an optional to specify the fields to be returned or what is called the projections. This method will return a cursor that can be used to iterator through all the returned documents. For more information about the cursor, you can have a look at [MongoDB documentations](https://docs.mongodb.org/manual/core/cursors/). In the following sections, I will show how you can use MongoDB to run parametrised, range, array, and embedded documents queries. 

#### Parameterised Queries

To query all the documents inside a particular collection, you need to specify the selection query and define projections if needed as seen in the below example:

````
db.products.find({"name":"Tshirt"})
````

In the above example we are querying the products collection to find all documents with name "Tshirt". If you don't specify any projections, the MongoDB will return all fields. If you want to return only product name, price and size , you can do it as shown below:



````
db.products.find({"name":"Tshirt"} , {name:1, price:1, size:1})
````

MongoDB will return the _id field by default, if you want to exclude the _id field you can change the query to below:

````
db.products.find({"name":"Tshirt"} , {name:1, price:1, size:1, _id:0})
````

You can also query documents using one of the query selectors as shown in the example below:

````
db.products.find({"name":{$in: ["Tshirt","TV"]}} , {name:1, price:1, size:1, _id:0})
````

In the above example, we are querying for all documents with names "Tshirt" and "TV". For a complete list for all the query selectors supported by MongoDB, please have a look at the [documentations](https://docs.mongodb.org/v3.0/reference/operator/query/).



#### Range Queries

Doing a range query in MongoDB is simple. You can just use the range query selectors such as $gt, $gte, $lt, and $lte in the query criteria to do any range queries. For example, to get all products with price in a certain range we do that as shown below:


````
db.products.find({"price":{$gt: 400, $lt: 1000}})
````

#### Query Arrays

If you want to query array fields inside a documents, you can simply check if the array values contains our query values as seen in the example below:

````
db.products.find({"tags":"Electronics"})
````

If you are querying an array of documents, you can use the $elemMatch query selector as shown below:

 
````
db.products.find({"manufacture": 
                               {
                                 $elemMatch:
                                       {
                                         name: Boss,
                                         Country: US
                                       }
                })
````

In the above example, we are search inside the manufactures list which is an array value. By using $elemMatch, we can look for a document inside the array with the exact fields.


#### Query Embedded Documents

To query inside the embedded documents, you can use the dot notation as shown in the example below:



````
db.products.find({"details.size":"20"})
````

In the above example we are searching for all products with size equal to 20 which is a subfield inside the product document.

You can also search for the exact sub document as shown below:


````
db.products.find({"details":{size:20 , colour: Red})
````
This will give you the exact match of the sub document, so the order of the fields is very important. This means the above query won't return below document:

````
{
_id: "SomeID",
name: "ProductName",
details: {colour: Red, size:20}
}
````




##### Built-in query functions

MongoDB supports many built in functions that can be applied during query operations such as skip(), limit(), min(), max(), distinct and count(). For a complete list of all the options, please have a look at [MongoDB documentations](https://docs.mongodb.org/v3.0/reference/method/js-cursor/). I will show below how to use the most popular options. 

##### count()

To get the count of all the documents in a query result set, you can directly use the count() method as shown below:

````
db.products.find({cat: "Electornics"}).count()
```

The above query will return the number of products in the electronics category. 


##### min()

Similarly, you can use the min() method to get the lower index bound of the returned result set as shown below:

````
db.products.find({cat: "Electornics"}).min({ price: 1.00 })
```

In the above query I am returning the product with minimum price in the electronics category. You need to define an index bound, as shown above we have used 1.00 as the index minimum bound.


##### max()

You can also use the max() method to get the upper index bound of the returned result set as shown below:

````
db.products.find({cat: "Electornics"}).max({ price: 2000 })
```

In the above query I am returning the product with maximum price of 2000 in the electronics category. 

##### skip()

This method is used if you want to skip some documents from the returned results set as seen below:


````
db.products.find({cat: "Electornics"}).skip(5)
```

In the above example, we are skipping the first 5 documents and then returning all other documents.


##### limit()

To return only a limited number for documents from the returned results set, a limit() method is supported as seen below:


````
db.products.find({cat: "Electornics"}).limit(5)
````

In the above example, we are returning only 5 documents from the results set.



##### distinct()

MongoDB supports also the distinct function to get all the unique values of a certain field across multiple documents in a collection. An example is given below:

````
db.products.distinct("size")
````

The above query will get the unique sizes across all products.












