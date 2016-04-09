[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Query Model > Sorting


MongoDB provide very useful method to sort the returned results of a query operation. The method sort() can be used by defining the fields to be used for sorting. An example is shown below:

````
db.products.find().sort({price: 1})
````

You can sort the collection documents using a certain field either ascending or descending by defining this field with 1 or -1 respectively. In the above example, we are sorting the products collection documents ascendingly by price. 

In general, you should create index in the field you are planning to use for sorting. Otherwise MongoDB is going to sort the documents in memory which requires your result set to be less than 32 MB. So usually if you want to sort without indexes, you should use the sort method with the limit function as shown below:

````
db.products.find().sort({price: 1}).limit(20)
````