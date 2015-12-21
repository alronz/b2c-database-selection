#### [back](search_data_main.md)

Index is a very important concept in any database to support efficient execution of queries. For instance, MongoDB must perform a full collection scan if we don't create index in one of the fields that we want to query. Indexes acts like the table of contents used to find a certain book in a very large book. MongoDB indexes are created using the efficient B-tree data structure to store small portion of information used to speed up queries by limiting the number of documents to be searched. These information will be stored using the B tree structure in order which is useful to perform range queries or sorting. 


MongoDB supports a variety of index types that are defined on the collection level and can apply to any field or subfield in the document. MongoDB supports the following index types:


#### Single Field Index

MongoDB supports creating indexes in any field or sub-field of a document inside a particular collection. By default, MongoDB creates a unique ascending single field index on the _id field of each document. This index acts like the primary key for a collection and can't be removed. You can create a single field index easily using the createIndex() method as seen below:

Product:

````
{
sku: "SomeProductSku",
"name": "ProductName",
size: "20",
price: "200",
address:
{
street: "StreetName"
city: "City",
country: "Country"
} 
tags: ["Stylish" ,"Top" ,"TShirt"]
},
sold: ["date1","date2","date3"]
````

Create index on the name field:

````
db.products.createIndex({"name":1})
````

In the above example we created an ascending/descending index on the name field of the products collection. If you want to create an index on a sub-field, you can use the dot notation as seen below:

````
db.products.createIndex({"address.street":1})
````

Or you can create an index on the whole sub-document as seen below:

````
db.products.createIndex({"address":1})
````

Then you can run queries like below:


````
db.products.find({"name": "ProductName" })
````

````
db.products.find({"address.street": "StreetName" })
````

````
db.products.find({"address": {street:"StreetName", city:"City", country: "Country"} })
````

````
db.products.find({"address": {street:"StreetName", city:"City", country: "Country"} })
````

#### Compound Index

MongoDB supports also compound indexes where you can create index to hold references to more than one field inside a document within a particular collection. The order of the fields is important since it makes a difference in the returned results. For example if you create the below compound index on the price and size fields of the products collection:


````
db.products.createIndex({"price":1, "size": 1})
````

This means that the index will first sort the products by price then for each price, it will sort by size.  You can then get run a sort query like below:

````
db.products.sort({"price":1, "size": 1})
````

MongoDB imposes a limit of 31 fields for each compound index.


#### Multikey Indexes

MondoDB supports also a multi-key indexes to index any contents stored inside arrays. A multi-key index will be created if you try to create an index in a field with array value.  MongoDB then will create index for each element inside the array. This type of indexes allows you to run queries on array of elements inside a document. An example would be of want to create an index on the tags field of the products collection as seen below:


````
db.products.createIndex({"tags":1})
````

If you want to create a compound index that contains a field of an array type, please note that you can use at most one multi-key index in the compound index. For example, if you want to create a compound index using the tags and sold fields as shown below, you will get an error:


````
db.products.createIndex({"tags":1,"sold":1})
````

The above index won't be created since more than one multi-key index needs to be created.


#### Geospatial Indexes

MondoDB supports two type of Geospatial indexes to allow for efficient queries of geospatial data. The first index is called 2d indexes which is used for planner geometry data. The second is called 2 sphere indexes which is used for spherical geometry data.

These indexes stores your data as GeoJSON objects and supports many types such as point, polygon , multipoint and many others. For more information about how to use these types of indexes, please have a look at [MongoDB documentations](https://docs.mongodb.org/manual/applications/geospatial-indexes/).



#### Text Index

MongoDB supports text indexes to allow for queries that performs only search on string contents. You can create a text index easily by using the "text" keyword as shown below:


````
db.products.createIndex({"name": "text"})
````

Like Multi-Key indexes, you are allowed to use only one text index inside the compound index. For more information about this index, please have a look at [MongoDB documentations](https://docs.mongodb.org/manual/core/index-text/).


#### Hash Index

Hash index is used to store the hash value of the really values of the indexed field. If the indexed field is a sub-document, then it will collapses the whole document and compute the hash for all fields inside this sub-document. This index is used when you want to shard a collection by certain hash shard keys. This type of index doesn't support multi-key indexes. You can create a hash index using the "hashed" keyword as seen below:

````
db.products.createIndex( { name: "hashed" } )
````



#### Index Properties 

You can define some properties for your index as explained below:

##### Unique

You can define a certain index as unique which means that there should be no other document with the same indexed value, otherwise MongoDB will reject this document.  You can make any index Unique as seen below:

````
db.products.createIndex( { "name": 1 }, { unique: true } )
````

If you do this, then any new created document with already existing name will be rejected.


##### Partial

This is a new property added on MongoDB version 3.2 that can be used when creating indexes and specify certain filter so that this index will be applied only to the documents that match this filter. Example is below:


````
db.products.createIndex( { "name": 1 }, { partialFilterExpression: { size: { $gt: 5 } } )
````

The above example will apply the index only on documents having size greater than 5.


##### Sparse

This property is like previous property but with only one filter criteria which is that we apply the index only if the indexed field exists. Example is given below:


````
db.products.createIndex( { "name": 1 }, { sparse: true } )
````

Using this property will make the index applies only for documents that are having a name filed. 


##### TTL

This property makes the document automatically removed by MongoDB after a pre-defined time.  This index is very useful for certain types of data such as temporary data that shouldn't be persisted in database. Examples are logs or event data. Below is an example of how to use this property:



````
db.eventlog.createIndex( { "lastModifiedDate": 1 }, { expireAfterSeconds: 3600 } )
````

In the above example, we are creating a TTL index on the lastModifiedDate field which will let MongoDB expire the document after 3600 seconds from the value of the lastModifiedDate fields.


##### Removing indexes

To remove an already created index, you can use the  db.collection.dropIndex() method.  For example if we want to remove the single field index we created on the name field of the products collection, we can run the below :

````
db.products.dropIndex({"name":1})
````







