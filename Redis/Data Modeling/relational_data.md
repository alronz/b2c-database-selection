
NoSQL databases are not always the best choice for relational data. When you think of using key-value database, you are usually looking for limited uses cases such as for cashing , session management or to maintain a queue, ect. However Redis can be also used to store application domain data and act as a complete data store. Although using Redis for data with complex relations isn't recommended, you can still use Redis for such scenarios. A small example will be if you want to store your customer order data where each customer have many orders. You can model it in Redis by store the customer details in a hash to be like a customer record in a relational database. For instance, customer1 can have its details stored in a hash with name customer:customer1. Then you can use a a set to store all the orders of customer1 e.g a set with name orders:customer1. We can even use a sorted set to store the same thing where the score can be the order id or a timestamp that will allow you to retreive the customer's orders ordered by time. A list also can be used with the same logic e.g a list with name orders:customer1 has all customer's orders. We will basicly store the orderID as string in the set,sorted list or list and the details of the order will be stored as in hashes where each hash will correspond to an order row in relational database. As you can see soring relational data inside Redis can get a little bis confusing but it is still possible to model it at the end. Below is the customer order example by code:

````
# Below are few customers set inside hashes:
> hmset customer:1 name John   ... more fields ...
> hmset customer:2 name Tom    ... more fields ...
> hmset customer:3 name Chris  ... more fields ...
# Below are few orders
> hmset order:1    ...  fields ...
> hmset order:2    ...  fields ...
> hmset order:3    ...  fields ...
> hmset order:4    ...  fields ...
> hmset order:5    ...  fields ...
# Defining one to many relations
customer:1 has made order:1 , Order:2
customer:2 has made order:3
customer:3 has made order:4, order:5
# For each customer, we keep the references to orders in a set
> sadd orders:customer1 1 2
> sadd orders:customer2 3
> sadd orders:customer3 4 5
# Then it will be easy to get the orders of a particular customer
> smembers orders:customer1
> 1) "1"
2) "2"
> hmget order:1 order:2
````

Above example was to show how to model many to many relation but modeling one to one relation or one to many will be achieved in the same way. 


#### Primary and compound key

The key in Redis that is used to access a certain value is the closest thing to the primary key in relational database. In the other hand, compound keys in relational database can be compared to using colons in earlier redis versions where only string where supported. Colons where used for storing namespace data, so accessing the email of the user will be defined as "set users:john:email abc@a.com". Currently you can use hashes to store such data so in the last example we will use a hash to store users where each user can have email as a field. When accessing this field, you need to get it by using two keys which is the hash key and the field key "hget myHashKey fieldKey".


#### Joins 

The closest concept in Redis to joins in relational database will be the use of operations such as intersect, union or difference in the sets. Sets supports below operations:

SDIFF which can be used to get the elements of a set that results from taking the  difference between the first set and all the following sets. SDIFFSTORE is the same but can store the result in a destination set. Example is given below:

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

SUNION is also used to return the elements of a set that results from the union of all the provided sets. SUNIONSTORE is the same but can store the result in a destinations set. Example is given below: 

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

Sorted sets also provides similar operations which are explained below:

ZINTERSTORE is used to calculate the intersection of some keys in sorted sets (you need to provide the number of keys) and stores the result in a destination sorted set. You can use two options for the resulted intersection either WEIGHTS or AGGREGATE. WEIGHTS option is used to specify a multiplication factor for each input sorted set which will be used to calculate the score in the resulted sorted set. AGGREGATE option is used to specify how the results of the intersection is aggregated examples are SUM (default) , MIN , or MAX which means that the resulting set will contain the minimum or maximum score across the inputs sets. Example is shown below:

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
