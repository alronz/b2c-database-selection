

### [Redis ](../Redis.md) > [Query Model](Query Model.md) > Query Options
___


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
In a sorted set data structure, you can query your data by both a key and a score:

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

Beside querying the data using the primary key, you can use secondary or compound keys as has been already explained in the [previous section.](Indexing.md)



##### Range Queries

Range queries is supported in Redis using sorted sets. For example if you have a product object stored in hash data structure, then you can easily get all products within a specific price range by storing the product Ids in a sorted set data structure with product prices as the sorted set scores. Then to do the price range query, you can get elements from the sorted set using scores range as seen below:

````
this.jedisClient.zrangeByScore("Product.Price.Index", min, max); 
````

In the above query, we are retrieving all product Ids within a min and a max price range. Then you can get the product details in the hash using the product Ids as seen below:

````
this.jedisClient.hgetAll("Products:" + productID);
````


##### Built-in query functions

In this section, I will talk about how Redis supports some common query functions such as count, max, min, sum, average, and others.

###### Count

To get the number of all elements inside a Redis data structure, different commands are supported for each data structures as shown below:

In the list data structure, the LLEN command will give you the length of the list as shown below:

````
(integer) 1
redis> LPUSH mylist "Hello"
(integer) 2
redis> LLEN mylist
(integer) 2
redis>
````

In the set data structure, the SCARD will give you the number of members in the set as shown below:

````
redis> SADD myset "Hello"
(integer) 1
redis> SADD myset "World"
(integer) 1
redis> SCARD myset
(integer) 2 
````

And in the sorted set data structure, the ZCARD command can be used to return the number of members inside the sorted set as shown below:

````
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZADD myzset 2 "two"
(integer) 1
redis> ZCARD myzset
(integer) 2
````

###### min and max

Usually min and max can be achieved using a sorted set. For instance, if you want to get the minimum and maximum price for a products, you will insert these products inside a sorted set with score as the product price. Then to get the minimum or maximum price, you will just get use the ZRANGEBYSCORE or ZREVRANGEBYSCORE commands to get the minimum and maximum prices. Example is shown below:

````
redis> ZADD myzset 100 "productOne"
(integer) 1
redis> ZADD myzset 90 "productTwo"
(integer) 1
redis> ZADD myzset 200 "productThree"
(integer) 1
redis> ZREVRANGEBYSCORE myzset 0 0
1) "productThree"
redis> ZRANGEBYSCORE myzset 0 0
1) "productTwo"
````

###### sum, average and similar queries

This kind of queries needs to be handled in the client side. For example if you want to get the sum price of all products in the cart, the client needs to get all products then go through all of them and sum the price.


