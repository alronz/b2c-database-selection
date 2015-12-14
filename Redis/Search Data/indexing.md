Since Redis is a key-value store, the key acts as the primary index in all data structure. Redis doesn't support internally other type of indexes such secondary and compound indexes. However, secondary and compound indexes can be implemented and maintained manually by the clients using sets and sorted sets.  In the below section I will show how to use primary, secondary and compound indexes in Redis.

Although creating secondary indexes and compound indexes is still possible somehow using sets and sorted sets, maintaining theses indexes manually at each write operation is not user friendly and is open for human errors. 


##### Primary Index

The key used to access any value in Redis acts as the primary index. An example is shown below:



Product object stored in a hash :

````
HashMap<String, String> map = new HashMap<String, String>();
		map.put("ProductID", "ThisIsProductID");
		map.put("SKU", "ThisIsSKU");
		map.put("Name", "shirt");
		map.put("Color", "Red");
		map.put("Price", "20");
		this.jedisClient.hmset("Products:" + map.get("ProductID"), map);
		
// get the product object using the key (primary index)
this.jedisClient.hgetAll("Products:" + productID);
````

In the above example we have used the productID as the primary key by using it as the key for the product hash object.


##### Secondary Index

If we want to access the product object shown in the previous example using other field in the hash beside the productID such as product colour, we can create a set for each colour that contains all product Ids having this colour as shown in the example below:

````
this.jedisClient.sadd("Product.Colour.Index:" + map.get("Colour"),
				map.get("ProductID"));
````

Then we can use this secondary index as shown below:

````
this.jedisClient.smembers("Product.Colour.Index:" + colour) ; // Then hgetAll for each productID
````			

You can also use a sorted sets to create secondary indexes in case you want to index numeric values such as the product price. An example is shown below:

````
this.jedisClient.zadd("Product.Price.Index",
				Double.valueOf(map.get("Price")), map.get("ProductID"));
````

In the above example, we use sorted set as a secondary index to be able to query easily for the product price. Each product id is mapped to price inside the sorted set which allows you later to do queries like the below:

````
this.jedisClient.zrangeByScore("Product.Price.Index", min, max); 
````

In the above query, we are returning all the products with price between a min and max values.

##### Compound Index

You can use sorted sets to do queries in two fields at the same time on the product stored in the hash data structure. To do that, you need to set the score of the sorted set all to zero which makes Redis sort the set lexicographically which then can compare two field at the same time. An example is shown below:

````
	this.jedisClient.zadd(
				"Product.Category.Price.Index",
				0,
				map.get("CategoryID") + ":" + map.get("Price") + ":"
						+ map.get("ProductID"));
````


In the above example, we are inserting the product id and the category id inside a sorted set and setting the score to zero. The order in the sorted set will be now a lexicographically order which means Redis is going to order the string having "categoryID:price:productID" byte by byte. So if you want to return all product Ids in the category id 2 between price 20 and 30, you can run the below query:


````
this.jedisClient.zrangeByLex("Product.Catgeory.Price.Index", "[2:20", "[2:30"); // get price between 20-30 for category 2
````
  
  






