#### [back](search_data_main.md)

MongoDB supports many query options that can be applied on query operations such as skip(), limit(), min(), max(), group, distinct and count(). For a complete list of all the options, please have a look at [MongoDB documentations](https://docs.mongodb.org/v3.0/reference/method/js-cursor/). I will show below how to use the most popular options. 

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


##### group()

The group command takes multiple documents and calculate a grouped results based on a certain field or fields. Example is given below:

````
db.products.group( {
key: {colour: 1},
cond: {price: {$lt:200}},
reduce: function(cur,result){result.quantity += cur.quantity}
initial: {quantity = 0}
})
````

In the above example we are grouping all products with price less than 200 by the colour field and getting the sum of quantities in each group.

##### distinct()

MongoDB supports also a distinct function to get all the unique values of a certain field across multiple documents in a collection. An example is given below:

````
db.products.distinct("size")
````

The above query will get the unique sizes across all products. 





