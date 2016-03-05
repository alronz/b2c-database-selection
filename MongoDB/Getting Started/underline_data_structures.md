#### [back](getting_started_main.md)

MongoDB stores its data as documents which is a data structure similar to the structures that maps values to keys such as hashes and dictionaries. These documents contain a JSON like data or what is called a BSON data. Many documents can be grouped into a one container called a collection which is similar to the table concept in the traditional databases. 

Below I will explain the three main concepts used in MongoDB which are the documents, collection and the BSON format.


#### Documents

All the data stored in MongoDB is stored in the form of a document. A document stores the data in a JSON-Like format called BSON which will be explained in more details later. This format stores the data as key-value pairs as seen in the below small example for a product object in an ecommerce application:

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

The "products" above is the name of the collection as will be explained in the following section.


#### Collections

A collection in MongoDB is an important concept which is used to group multiple documents. If we say that a document in MongoDB is similar to a row in tradition databases then the collection is analog of a table. 

A collection in MongoDB can contain document with different schema or what is called a dynamic schema support. This means that inside a single collection we can store documents with completely different values as shown in the example below:

````
{sku:"xxxx" , productName: "Name" , price: "200"}
{categoryID: "xxxx" , categoryName:"CategoryName"}
````

As seen above we can store a product document and a category document in the same collection. However it makes sense only to store similar documents in the same collection for the following reasons:

* For grouping and querying reasons, it would be easier to query a collection with a similar structure. For example if you want to query all products that are having the same manufacturer, you just query the "products" collection.
* Grouping similar documents in the same collection allows for data locality.
* You can index a collection more efficiently.


You can use also namespace to define a collection for grouping purposes. For example you define a collection with the name history.orders. This doesn't mean that you can use a sub collection, it is just a collection name and there is no difference between historyOrders or history.orders. 


#### BSON Format

MongoDB uses a BSON format to represent its documents. BSON is a binary encoded format that extends the well-known JSON model to provide more data types support. BSON also is a lightweight efficient format. It is also very fast and traversable. MongoDB can support embedding objects and arrays just like JSON but can also give access to its inside objects which is used by MongoDB to build indexes and nested keys. BSON can support many data types as can seen in more details in [MongoDB documentation](https://docs.mongodb.org/manual/reference/bson-types/). I will give below a brief description for the common data types used in MongoDB:


##### Double, String, Boolean, Date and Timestamp

MongoDB supports the common data types that are used on most programming languages such double, boolean, string, and Date. The timestamp data type is a special timestamp type used internally by MongoDB which is not associated with the Date type. The timestamp is a 64 bit value where the first 32 bits stores the seconds since the Unix epoch and the second 32 bits stores an incrementing ordinal that are used for operations within a given second. Timestamps are always unique. However the Date data type is a 64-bit integer that contains the number of milliseconds since the Unix epoch.

##### Object, Object id

The object data type stores an embedded document and the object id is a unique key consists of 12-bytes, and the first four bytes are a timestamp that reflects the ObjectId’s creation date.


##### Array and Binary data

You can store an array inside a MongoDB BSON document that can contains other embedded documents or any other data types values. MongoDB supports also storing binary data such as an image or a file inside the BSON document.


##### Regular Expressions

This datatype is used to store regular expression.

##### Other data types:

Min/ Max keys : This is used if you want to compute a value against either the min or max BSON fields.

JavaScript : This datatype is used if you want to store some javascript code into the BSON document.







