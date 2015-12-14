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
















