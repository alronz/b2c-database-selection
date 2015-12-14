#### [back](search_data_main.md)

MongoDB supports regular expressions for pattern matching strings in query operations. MongoDB supports a huge number of regular expressions. You can use the $regex keyword to specifiy regular expression matching patterns as shown below:

````
db.products.find( { sku: { $regex: /^ABC/i } } )
````

For a complete list of all the regular expressions options that you can use, please have a look at [MongoDB documentations](https://docs.mongodb.org/manual/reference/operator/query/regex/). 
