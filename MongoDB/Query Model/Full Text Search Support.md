[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Query Model > Full Text Search Support


MongoDB supports Full-Text Search using Text Indexes. Text Indexes can be defined on any string or array of strings values in your documents that you want them to be fully searched. By defining a text index on any field, MongoDB will tokenises and stems the field's text content and creates the index accordingly. 

To create a text index on any field, you need to specify the "text" keyword. You can create only one text index per collection. If you want to index multiple fields of a document, you can create a compound text index. 

As for any full text functionality, MongoDB text index assign some score to each document and then you can sort the search results by this score to show the more relevant results. You can also see the search score for each returned document by using the { $meta: "textScore" } expression.


For instance, if we want to search products by product name. We can create a text index on the product name field as shown below:

````
db.products.createIndex({"productName":"text"})
````

Then if you want to do a full text search in all the products having the keyword "shirt", we can run the below find query:

````
db.products.find({$text: {$search: "shirt"}}, {score: {$meta: "textScore"}}).sort({score:{$meta:"textScore"}})
````

As seen above, the query will sort results by their search relevancy and will return the score for each document.

In case you want to do a full-text search in the product names and descriptions, you can create a compound text index as seen below:


````
db.products.createIndex({"productName":"text", "productDesc":"text" })
````


Then when you search for a keyword, the product names and descriptions will be fully searched. 


In some use cases, you might be interested to do a full-text search for all the fields of a document. Then you can use the Wildcard Indexing supported by MongoDB. An example would be if you want to index all the fields of an email document. Then you can do this as shown below:

````
db.emails.createIndex({"$**":"text"})
````

If you want to do a full text search on a certain phrase, you can either choose to do an OR for all the keywords inside the phrase or to do an AND for the keywords as shown below:


Below the result will be computed by doing an OR operation for all the keywords inside the phrase:

````
db.products.find({$text: {$search: "nice branded shirt"}}, {score: {$meta: "textScore"}}).sort({score:{$meta:"textScore"}})
````

Or use double quotes in the search phrase then the result will be computed by doing an AND operation for all the keywords inside the phrase:

````
db.products.find({$text: {$search: "\"nice branded shirt\""}}, {score: {$meta: "textScore"}}).sort({score:{$meta:"textScore"}})
````


Finally, if you want to assign some weight for the text indexed fields in case you are having compound text index. Then you can assign the weights as shown below:

````
db.products.ceateIndex( {"productName": "text", "productDesc":"text" }, {"weights": { productName: 3, productDesc:1 }} )
````

Then the score for the documents having the searched keyword in the product name field will be more than the documents having them in the product description field.
