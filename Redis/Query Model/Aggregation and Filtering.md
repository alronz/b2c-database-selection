[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Query Model > Aggregation and Filtering


###### Aggregation

In general, aggregation isn't internally supported by Redis but there is a workaround to group data in Redis using sets and sorted sets. In this section I will show how you can group your data in Redis.


To group your data in Redis, the set data structure is the best candidate since you will be able to easily insert elements from the same group type as members of the set. An example is if you want to group all products that were produced by a certain manufacturer. 

````
this.jedisClient.sadd("Product.Manufacture.Index:" + map.get("Manufacture"),
				map.get("ProductID"));
````


In the above example, we created a set for each manufacturer and then inserted all product Ids of the products produced by this manufacturer. Then to get all the products produced by this manufacturer, we can run a query like below:

````
this.jedisClient.smembers("Product.Manufacture.Index:" + manufacturer) ;
````

 
###### Filtering


To filter data in Redis, you can use the intersection, union, and difference functionalities provided by the set and sorted sets. For example if you want to get all products that are produced by a certain manufacturer and having the red colour, then you can intersect the "Product.Manufacture.Index:manufacturer" and "Product.Color.Index:colour" sets as shown below:

 ````
 this.jedisClient.sinter("Product.Manufacture.Index:Boss" , "Product.Color.Index:Red");
 ````
 
 In the same way, you get all products from both sets using the union command as shown below:
 
 
 ````
this.jedisClient.sunion("Product.Manufacture.Index:Boss" , "Product.Color.Index:Red");
 ````
 
 And to get the products that are in the first set but not in the second set, you can use the difference command as shown below:
 
````
this.jedisClient.sdiff("Product.Manufacture.Index:Boss" , "Product.Color.Index:Red");
````
 
