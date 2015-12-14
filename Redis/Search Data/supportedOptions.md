#### [back](search_data_main.md)

In this section, I will talk about how Redis supports some common query options such as count, max, min, sum, average , and others.

##### Count

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

##### min and max

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

##### sum, average and similar queries

This kind of queries needs to be handled in the client side. For example if you want to get the sum price of all products in the cart, the client needs to get all products then go through all of them and sum the price.
