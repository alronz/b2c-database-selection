Redis is a key-value database which means you can query your data easily using the keys. In this section I will show how range and parameterised queries are supported.


##### Parameterised Queries

If you want to run parameterized queries in the data stored inside the different Redis data structures, you can easily query them using the data structure key. Examples for each data structure is given below:

A string data structure:

````
redis> SET mykey "Hello"
OK
redis> GET mykey
"Hello"
````

In a list data structure, you can query a certain element by index:

````
redis> LPUSH mylist "world"
(integer) 1
redis> LPUSH mylist "hello"
(integer) 2
redis> LINDEX mylist 0
"Hello"
````

In a set data structure, you can only query using the set key and a certain member to check if the member exists inside the set or not :

````
redis> SADD myset "one"
(integer) 1
redis> SISMEMBER myset "one"
(integer) 1
````
In a sorted set data structure, you can query your data by key and score:

````
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZSCORE myzset "one"
"1"
````

In a hash data structure, you can query your data by the hash key and fields inside each hash as shown below:

````
(integer) 1
redis> HGET myhash field1
"foo"
redis> HGET myhash field2
(nil)
redis> HGETALL myhash
(all fields and values)
````

Beside querying the data using the primary key, you can use secondary or compound keys as has been explained in the [previous section.](indexing.md)



##### Range Queries

Range queries is supported in Redis using sorted sets. For example if you have a product object stored in hash data structure, then you can easily get all products within a specific price range by storing the product Ids in a sorted set data structure with product prices as the sorted set scores. Then to do the price range query, you can get elements from the sorted set using scores range as seen below:

````
this.jedisClient.zrangeByScore("Product.Price.Index", min, max); 
````

In the above query, we are retrieving all product Ids within a min and max price range. Then you can get the product details in the hash using the product Ids as seen below:

````
this.jedisClient.hgetAll("Products:" + productID);
````



