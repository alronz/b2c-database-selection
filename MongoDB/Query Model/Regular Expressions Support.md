[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Query Model > Regular Expressions Support


MongoDB supports regular expressions for pattern matching strings in query operations. MongoDB supports a huge number of regular expressions. You can use the $regex keyword to specifiy regular expression matching patterns as shown below:

````
db.products.find( { sku: { $regex: /^ABC/i } } )
````

For a complete list of all the regular expressions options that you can use, please have a look at [MongoDB documentations](https://docs.mongodb.org/manual/reference/operator/query/regex/). 
