MongoDB stores its data in a document which is a data structure similar to the structures that maps values to keys such as hashes and dictionaries. These documents contain a JSON like data or what is called a BSON data. Many documents can be grouped into a one container called a collection which is similar to the table concept in the traditional databases. 

Below I will explain the three main concepts used in MongoDB which are the documents, collection and the BSON format.


#### Documents

All the data stored in MongoDB is stored in the form of a document. A document stores the data in a JSON-Like format called BSON which will be explained in more details later. This format stores the data as field-and-value pairs as seen in the below small example for a product object in an e commerce application:

````
{
  sku: "xxxxx",
  type: "TShirt",
  title: "Stylish TShirt ",
  description: "by Boss",

  shipping: {
    weight: 6,
    dimensions: {
      width: 10,
      height: 10,
      depth: 1
    },
  },

  pricing: {
    list: 1200,
    retail: 1100,
    savings: 100,
    pct_savings: 8
  },

  tags: [
      "Shirt",
      "Clothes",
      "Summer",
      "Stylish"
    ],
}
````
As you can see above, the document in MongoDB acts as a row in the traditional database. The above document can store values in many data types, for example sku,type and description are of type String. Shipping field is a document type that contains another nested document, and tags field is an array.  However the fields are always of String data type.  

The documents can grow to a very large size but the maximum size is limited to 16 MB. This ensures the document won't grow to a degree that can use excessive amount of memory or excessive amount of bandwidth during transmission. 

MongoDB creates by default a unique index on the _id of each document. The _id is usually a unique identifier for each document and can be of any BSON data type except for array. 


You can access any field in the document using the dot notation as shown in the example below to access the sku of a particular product:

````
db.products.product.sku
````

The products above is the name of the collection as will be explained in the following section.


#### Collections

A collection in MongoDB is an important concept which is used to as a group of multiple documents. If we say that a document in MongoDB is similar to a row in tradition databases then the collection is analog of a table. 

A collection in MongoDB can contain document with different schema or what is called a dynamic schema support. This means that inside a single collection we can store documents with completely different values as shown in the example below:

````
{sku:"xxxx" , productName: "Name" , price: "200"}
{categoryID: "xxxx" , categoryName:"CategoryName"}
````

As seen above we can store a product document and a category document in the same collection. However it makes sense only to store similar documents in the same collection for the following reasons:

* For grouping and querying reasons, it would be easier to query a collection with a similar structure. For example if you want to query all products that are having the same manufacture, you just query the products collection.
* Grouping similar documents in the same collection allows for data locality.
* You can index a collection more efficiently.


You can use also namespace to define a collection for grouping purposes. For example you define a collection with the name history.orders. This doesn't mean that you can use a sub collection, it is just a collection name and there is no difference between historyOrders or history.orders. 


#### BSON Format

MongoDB uses a BSON format to represent its documents. BSON is a binary encoded format that extends the well-known JSON model to provide more data types support. BSON also is a lightweight efficient format. It is also very fast and traversable. MongoDB can support embedding objects and arrays just like JSON but can also give access to its inside objects which is used by MongoDB to build indexes and nested keys. BSON can support many data types as can seen in more details in [MongoDB documentation](https://docs.mongodb.org/manual/reference/bson-types/).


