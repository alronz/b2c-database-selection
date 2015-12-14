MongoDB provides normal CRUD operations to interact easily with your stored data. These operations can run only at the collection level. In the following sections I will talk briefly how to query or modify your stored data using MongoDB.


#### Query Data

You can easily retrieve the documents stored in a certain collections in MongoDB using the db.collection.find() method. The method accepts a query criteria and projection to return your data in a form of an iterator or a cursor and you can also apply some modifiers such as skips, sorts, and limits. An example is shown below:

````
db.products.find( 
{size:{$gt: 20}}, // The query
{ProductName:1, Price: 1, _id: 0 } // The projection 
).limit(10) // A modifier
````

As seen in the example above, we want to find all products with size 20 but we want to return only the first 10 documents and only the product name and price. By default MongoDB returns the _id field and can be excluded in the projection as seen above. 

MongoDB provides many options to query your data and will be covered in more details when talking about [how to search your data](../Search Data/query.md).

#### Create Data

To create new documents, MongoDB provides three methods as explained below:

##### db.collection.insertOne()

This method inserts only a single document in a certain collection. It has a simple structure as seen in the below example:


````
db.products.insertOne( 
{name:"TShirt", size:20 , price: 30 } // The document to be inserted
)
````
The _id will be generated automatically by MongoDB if not specified in the inserted document. 

##### db.collection.insertMany()

This method will insert many documents into the collection. You need just to specify an array of documents to be inserted as seen in the example below:

````
db.products.insertOne([
{name:"TShirt", size:20 , price: 30 },
{name:"TV", size:150 , price: 200 },
{name:"Phone", size:100 , price: 120 },
{name:"Laptop", size:170 , price: 500 }
])
````

##### db.collection.insert()

This is a general method to insert a single document or multiple documents into a certain collection as seen in the example below:

````
db.products.insert( 
{name:"TShirt", size:20 , price: 30 } 
)

// Or

db.products.insert([
{name:"TShirt", size:20 , price: 30 },
{name:"TV", size:150 , price: 200 },
{name:"Phone", size:100 , price: 120 },
{name:"Laptop", size:170 , price: 500 }
])
````

There are other methods that can create documents in MongoDB such as updateOne(), updateMany(), and replaceOne(). These methods will create a new document it couldn't find a document with the same specified filter and when their "upsert" option is set to true.



#### Update Data

Similar to creating data in MongoDB, there exists three different methods to update documents in a certain collection as will be explained below:

##### db.collection.updateOne()

This method will update a single document in a certain collection based on a particular filter and action as seen in the example below:


````
db.products.updateOne( 
{size: {$lt: 20}},  // This is the filter 
{$set:{type : "large" } } // The action to be applied 
)
````

In the above example, we are updating the first document returned if we apply a filter to get products with size less than 20. Then we update the type field to "large". We can also use other actions such as $unset to delete a particular field from a document or $rename to rename the field name which are very useful actions. 

##### db.collection.updateMany()

This method can be used to update multiple documents in a collection at the same time. Example is shown below:

````
db.products.updateMany( 
{size: {$lt: 20}}, 
{$set:{type : "large" } } 
)
````

This is the same example we used in updateOne method but it will now update all the documents returned by the filter instead of only the first document.

##### db.collection.update()

This is the general method and can be used to either update a single document or multiple documents inside a certain collection based on a particular filter and action. Both examples we saw previously can be used here with update method but we need to specify whether we want to update a single document or multiple documents using the 'multi' option  as seen in the example below:


````
db.products.update( 
{size: {$lt: 20}}, 
{$set:{type : "large" } },
{multi: false} 
)

// Or

db.products.update( 
{size: {$lt: 20}}, 
{$set:{type : "large" } },
{multi: true} 
)

````

MongoDB also provides two useful commands if you don't want just to update some values in a single document rather you want to replace the whole document. These command are explained below:

##### db.collection.replaceOne()

This method is used to replace a single document in a collection that was returned based on a particular filter. In this method, you don't need to specify an action but you need to give a new complete document to be replaced with the old one. Example is shown below:


````
db.products.replaceOne( 
{size: {$lt: 20}}, 
{name:"TShirt", size:20 , price: 30 } // The new document
)
````

##### db.collection.findOneAndReplace()

This method is similar to the previous one but it offers a sorting of the filtered result which gives you more control offer which document to replace. Example is if you want to replace only the product with the minimum price with size less than 20, you can do that as seen below:


````
db.products.findOneAndReplace( 
{size: {$lt: 20}}, 
{name:"TShirt", size:20 , price: 30 },
{ sort: { "price" : 1 } }
)
````


#### Delete Data

MongoDB also offers similar methods to delete documents from a particular collection based on a particular filter. I will explain below briefly how to use these methods.


##### db.collection.deleteOne()

This method is used to delete a single document from the collection based on a certain filter. The first document in the returned results will be deleted. An example is shown below:

````
db.products.deleteOne( 
{size: {$lt: 20}}
)
````

In the above example we are removing the first document returned if we apply a filter to get all products with size less than 20.

##### db.collection.deleteMany()

This method can be used to delete multiple documents at the same time from a certain collection and based on a particular filter. The previous example can still apply here, however all the documents with size less than 20 will be deleted:

````
db.products.deleteMany( 
{size: {$lt: 20}}
)
````

##### db.collection.remove()

This is the general method used by MongoDB to deleted documents from a collection based on a certain filter. You can use it to delete either a single or multiple documents by using the "justOne" option which is by default set to false.

````
db.products.remove( 
{size: {$lt: 20}}, 
{justOne: true} 
)

// Or

db.products.remove( 
{size: {$lt: 20}}, 
{justOne: false} 
)

````

To remove all documents in the products collection, you can call the below method:

````
db.products.remove( { } )
````

Here the filter is empty set which will return all documents and remove them all.  

db.collection.findOneAndDelete() is also available to find a single document based on a filter and sorting criteria and remove it.


Another general method used by MongoDB is db.collection.save() which can either updates an existing document if the document exists or creates a new document if it doesn't exist.


Finally, you can search for a document and modify it in the same round trip atomically by using db.collection.findAndModify. This method is very useful especially in transaction related tasks since it is like you are runnung a find and modify command atomically at the same time. More details about this method are nicely explained in [MongoDB documentation.](https://docs.mongodb.org/manual/reference/method/db.collection.findAndModify/)


