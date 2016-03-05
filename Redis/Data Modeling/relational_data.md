#### [back](data_modeling_main.md)


NoSQL databases are not always the best choice when it comes to relational data. When you think of using key-value database, you are usually looking for limited use cases such as for caching, session management or to maintain a queue. The same applies for Redis, however it can still be used to store application domain data because it has a built-in persistency. Using Redis for complex relational data isn't usually recommended, however Redis supports modelling data relationships as will be explained in this section. 

A small example is if you want to model the customer order one-to-many relationship where each customer have many orders. You can model it in Redis by storing the customer and order details in a hash to act like a table row in RDMS. Then you can use a set to store all the order ids of a particular customer. Additionally, we can use a sorted set to store the same thing where the score can be a timestamp that will allow us later to retrieve customer orders sorted by time.  

````
// Below are few customers set inside hashes:
> hmset customer:1 name John   ... more fields ...
> hmset customer:2 name Tom    ... more fields ...
> hmset customer:3 name Chris  ... more fields ...

// Below are few orders
> hmset order:1    ...  fields ...
> hmset order:2    ...  fields ...
> hmset order:3    ...  fields ...
> hmset order:4    ...  fields ...
> hmset order:5    ...  fields ...

// Defining one to many relations
// customer:1 has made order:1 , Order:2
// customer:2 has made order:3
// customer:3 has made order:4, order:5
// For each customer, we keep the references to orders in a set
> sadd orders:customer1 1 2
> sadd orders:customer2 3
> sadd orders:customer3 4 5

// Then it will be easy to get the orders of a particular customer
> smembers orders:customer1
> 1) "1"
2) "2"

// then get the order details
> hmget order:1 order:2
````

In the above example, we show how to model a one-to-many relationship. However modelling one- to-one or many-to-many relationships are achieved in similar way. 



#### Joins 

The closest concept in Redis to joins in relational database will be the use of operations such as intersect, union or difference in the sets. Sets supports below operations:

SDIFF which can be used to get the elements of a set that results from taking the difference between the first set and all the following sets. SDIFFSTORE is the same but can store the result in a destination set. Example is given below:

````
redis> SADD key1 "a"
(integer) 1
redis> SADD key1 "b"
(integer) 1
redis> SADD key1 "c"
(integer) 1
redis> SADD key2 "c"
(integer) 1
redis> SADD key2 "d"
(integer) 1
redis> SADD key2 "e"
(integer) 1
redis> SADD key3 "b"
(integer) 1
redis> SDIFF key1 key2 key3
1) "a"
````

SINTER is another set command that is used to return the elements of a set that results from the intersection of all the provided sets. SINTERSTORE is the same but can store the result in a destinations set. Example is given below:

````
redis> SADD key1 "a"
(integer) 1
redis> SADD key1 "b"
(integer) 1
redis> SADD key1 "c"
(integer) 1
redis> SADD key2 "c"
(integer) 1
redis> SADD key2 "d"
(integer) 1
redis> SADD key2 "e"
(integer) 1
redis> SINTERSTORE key key1 key2
(integer) 1
redis> SMEMBERS key
1) "c"
````

SUNION is used to return the elements of a set that results from the union of all the provided sets. SUNIONSTORE is the same but can store the result in a destinations set. Example is given below: 

````
redis> SADD key1 "a"
(integer) 1
redis> SADD key1 "b"
(integer) 1
redis> SADD key1 "c"
(integer) 1
redis> SADD key2 "c"
(integer) 1
redis> SADD key2 "d"
(integer) 1
redis> SADD key2 "e"
(integer) 1
redis> SUNION key1 key2
1) "a"
2) "b"
3) "c"
4) "d"
5) "e"
````

Sorted sets provide similar operations which are explained below:

ZINTERSTORE is used to calculate the intersection of some keys in sorted sets (you need to provide the number of keys) and stores the result in a destination set. You can use two options for the resulted intersection either WEIGHTS or AGGREGATE. WEIGHTS option is used to specify a multiplication factor for each input in the sorted set which will be used to calculate the score in the resulted set. AGGREGATE option is used to specify how the results of the intersection is aggregated. Examples are SUM (default), MIN, or MAX which means that the resulting set will contain either the sum, minimum or maximum score across the input sets. Example is shown below:

````
redis> ZADD zset1 1 "one"
(integer) 1
redis> ZADD zset1 2 "two"
(integer) 1
redis> ZADD zset2 1 "one"
(integer) 1
redis> ZADD zset2 2 "two"
(integer) 1
redis> ZADD zset2 3 "three"
(integer) 1
redis> ZINTERSTORE out 2 zset1 zset2 WEIGHTS 2 3
(integer) 2
redis> ZRANGE out 0 -1 WITHSCORES
1) "one"
2) "5"
3) "two"
4) "10"
````

ZUNIONSTORE is the same as ZINTERSTORE explained above but instead it calculate the union of multiple input sorted sets. It uses also the same options (WEIGHTS/AGGREGATE). Example is given below:

````
redis> ZADD zset1 1 "one"
(integer) 1
redis> ZADD zset1 2 "two"
(integer) 1
redis> ZADD zset2 1 "one"
(integer) 1
redis> ZADD zset2 2 "two"
(integer) 1
redis> ZADD zset2 3 "three"
(integer) 1
redis> ZUNIONSTORE out 2 zset1 zset2 WEIGHTS 2 3
(integer) 3
redis> ZRANGE out 0 -1 WITHSCORES
1) "one"
2) "5"
3) "three"
4) "9"
5) "two"
6) "10"
````
