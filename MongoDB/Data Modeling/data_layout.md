Unlike relational databases where you should define a table schema before inserting any data, MongoDB is schemaless and its collections don't enforce documents structure. Hence data modelling is more flexible and you have more choices to make. 

How to model your data into MongoDB's documents is defined based on many criteria and depended a lot on your application requirements and the underline data structure.  Important criteria is what your data access patterns such as how do you query and update your data. Another criteria are how is your data look like and what are your application needs.


Usually the most important question when modelling your data using MongoDB is how to represent the relationships between the data. There are two ways to represent the relationships between your data, either by preferring the normalisation way and modeling your data using references or by accepting some denormalization of your data and using embedded documents. 


#### Modelling relations using references

You can store a reference in one document to act like a link to another document, then the client can use this reference to retrieve the other document when needed. Using references can normalise your data and allows for more flexibilities in data modelling.

For example, if we have a product and category documents as seen below:

Categories:

````
{
	id: “1”,
	name: “Dell Laptops”,
	parent: “Laptops”,
}


{
	id: “2”,
	name: “Computers & Laptops”,
	parent: “Electronics”,
}
````

Product:

````
{
	sku: “SomeSkuID”,
	name: “Dell XPS”,
	size: “20”,
	colour: "Red",
	cat: [ "1" , "2"]
}
````

As you can see, we are referencing a product to category using the category ID. 

We usually use normalised data model using references as seen above if we want to prevent duplicating our data across documents. For instance, in the above example we are not mentioning the category information in each product inside this category. Instead we store just a reference to the category document and the client can use this reference to retrieve the category information if needed. 

Another important benefit of modelling data by references is the flexibility that you will get to model complex relations such as many-to-many relationships or large hierarchical data.

However the downside of using references is that the client side application needs to do a follow-up queries to resolve these references which might impact performance or increase the client side code and complexity.


#### Modelling relations using embedded documents

Since MongoDB allows you to embed related data in a single document, you can design your model using the denormalised model pattern. Using this pattern you can take full advantage of MongoDB's rich documents. Instead of using references to other related documents, you can embed these documents in a single document so that the client need to issue fewer queries and updates. 

An example is to embed the customer's address in the customer's document instead of storing it in a separate document as seen below:


````
{
	firstName: “John”,
	lastName: “Robert”,
	phoneNumber: “121212”,
	email: "email@yahoo.com",
	address: 
	{
	street: “street 1”,
	city: “Munich”,
	country: "Germany",
	postCode: "212"
	}
}
````

It is usually a good idea to store one-to-one relationships as embedded documents as seen in the example above. Also if you have a one-to-many relationship, you can store the "many" documents inside the "one" document as an array for embedded documents. 

In general, modelling relations using embedded documents will give a better read performance and you can retrieve related data only in a single read operation. 

Another important benefit is that you can update related data in a single atomic write operation which can be used to support transaction related tasks. 

However, a downside of using embedded document is when your document grows and becomes very large. This will cause performance issue in the storage engine such as MMAPv1 and might lead to data fragmentation. Because of this, it is not a good idea to use embedded documents when you think that these embedded documents can grow to an unbounded limit since this might cause problems in the future when your data becomes larger. 






