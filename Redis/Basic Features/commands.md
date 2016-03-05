#### [back](basic_features_main.md)

In order to do operations on the values stored in Redis such as add, update, get or remove, a set of commands for each data type are provided. Those commands can be executed on [bulk](Pipline support.md) and a partial [transaction](transaction_support.md) is supported  as will be explained later in details. Executing these commands can be done using the built-in client "redis-cli" or by using one of the supported [clients](http://redis.io/clients) specific for many programming languages. Redis has a relatively short list of commands that can be easily learned in few hours. In the following sections I will explain the popular commands for each data type including commands on the "key". For a complete list of all the commands available in Redis, please check [Redis documentation.](http://redis.io/commands).


#### Commands on Key

We start with the "DEL" command that can be used to delete a single or multiple keys as shown below. A key is ignored if it doesn't exist. 

````
redis> DEL key1 key2 key3
(integer) 3
````

"EXISTS" is another command that can be used to check if a single or multiple keys exists.


````
redis> EXISTS key1 key2
(integer) 2
````

"KEYS pattern" is used to search keys and returns all keys that match the given pattern. You should pay attention when using this command on a production environment since it can severely impact the system performance because it has a time complexity of O(N).

````
redis> KEYS *o*
1) "two"
2) "four"
````

"RENAME" is used to rename the key. "RENAMENX" is another command used to rename key only if the new key name doesn't exists.


````
redis> RENAME mykey myotherkey
OK
````

````
redis> RENAMENX mykey myotherkey
(integer) 0 // myotherkey already exists
````
"TYPE" is a command to get the underline data type of a particular key.

````
redis> TYPE key1
string
redis> TYPE key2
list
````

#### Commands on String

"GET" and "SET" are used to retrieve/set the value of a certain key. When you use "SET", it will overwrite the value if it already exists. However "SETNX" will set the value only if it doesn't exist. Other similar commands are "MGET,MSET,MSETNX" which are used to get or set multiple keys at the same time.

````
redis> SET mykey "Hello"
OK
redis> GET mykey
"Hello"
redis> SET mykey "World"
OK
redis> GET mykey
"World"
redis> SETNX mykey "AnotherWord"
(integer) 0
redis> SETNX otherKey "AnotherWord"
(integer) 1
````

"INCR,DECR" are two commands that can be used to increment or decrement the the integer/floating number stored inside the string by one. "INCRBY,DECRBY" can also be used to increment or decrement by a certain value.

````
redis> SET mykey 10
OK
redis> INCR mykey
(integer) 11
redis> DECR mykey
(integer) 10
redis> INCRBY mykey 2
(integer) 12
redis> DECRBY mykey 2
(integer) 10
````

"GETRANGE,SETRANGE" are similar to substr and replace functions in javascript which get or set a substring stored in a key based on a particular range.

````
redis> SET mykey "This is a string"
OK
redis> GETRANGE mykey 0 3
"This"
redis> SETRANGE mykey 0 "That"
(integer) 16
redis> GET mykey
"That is a string"
````
Finally the "APPEND" command is used to appends a value to the end of a string if the string already exists.

````
redis> APPEND mykey "Hello"
(integer) 5
````

* #### Commands on Lists

"LPOP,LPUSH,RPOP,RPUSH" are the most important commands on lists which are used if your want to insert or remove the first or last element in the list. "BLPOP,BLPUSH,BRPOP,BRPUSH" are doing the same but are the blocking version which means it blocks the connection when there are no elements to pop or push from the given list.

````
redis> RPUSH mylist "one"
(integer) 1
redis> RPUSH mylist "two"
(integer) 2
redis> RPUSH mylist "three"
(integer) 3
redis> LPOP mylist
"one"
redis> RPOP mylist
"three"
````

"LRANGE" is used to get elements within a specific range.

````
redis> RPUSH mylist "one"
(integer) 1
redis> RPUSH mylist "two"
(integer) 2
redis> RPUSH mylist "three"
(integer) 3
redis> LRANGE mylist 0 0
1) "one"
````

"LLEN" returns the length of the list.

````
redis> LLEN mylist
(integer) 3
````

"LTRIM" is used to remove elements from the list by a certain range.

````
LPUSH mylist someelement
LTRIM mylist 0 99 // Remove the first 100 elements of the list
````

"LINDEX" to get an element at a specific index. This operation has a time complexity O(N) and might be slow for large lists.

````
redis> LINDEX mylist 0
"Hello"
````


#### Commands on Hashes

"HGET,HSET,HMGET,HMSET" commands are similar to string commands, they can be used to get or set a single or multiple value(s) of a field(s) inside the hash. "HSETNX" is also used to set a value of a field inside the hash only if it doesn't exist.

````
redis> HSET myhash field1 "foo"
(integer) 1
redis> HGET myhash field1
"foo"
````

"HGETALL" is used to get all fields and their values inside a hash while "HKEYS" or "HVALS" are used to get only either all the field keys or all the values.

````
redis> HGETALL myhash
1) "field1"
2) "value1"
3) "field2"
4) "value2"
redis> HVALS myhash
1) "value1"
2) "value2"
redis> HKEYS myhash
1) "field1"
2) "field2"
````

"HEXISTS" is used to check if a certain field has a certain value, "HINCREBY" or "HINCREBYFLOAT" are used to increment the integer or float value stored inside a certain field. Finally HDEL is used to delete a specific field inside the hash.

````
redis> HEXISTS myhash field1
(integer) 1
redis> HINCRBY myhash field 1
(integer) 6
redis> HDEL myhash field1
(integer) 1
````

#### Commands on Sets

"SADD" is used to add a specific member to the set. If the member is already in the set, the operation will be just ignored. "SISMEMBER" is then used to check if an element is inside the set or not. You can use "SMEMBERS" to get all the elements inside the set and "SCARD" to get the number of members in the set.

````
redis> SADD myset "Hello"
(integer) 1
redis> SISMEMBER myset "Hello"
(integer) 1
redis> SMEMBERS myset
1) "Hello"
redis> SCARD myset
(integer) 1
````

"SDIFF,SDIFFSTORE,SINTER,SINTERSTORE,SUNION,SUNIONSTORE" are very important commands that can be executed on the sets to do group or filter the data inside different sets which will be covered on more details when discussing grouping and filtering data [later](../Search Data/filtering and grouping.md).

#### Commands on Sorted Sets

"ZADD" is used like normal sets to add elements to the sorted set but we should also provide a score for each element. The scroe can be used later to sort the set, and do range queries on the set.

````
redis> ZADD myzset 2 "two" 3 "three"
(integer) 2 
````

"ZCARD" can be used to get the number of elements inside the sorted set but "ZCOUNT" get the number of elements with score between a certain range.

````
redis> ZADD myzset 1 "one"
(integer) 1
redis> ZADD myzset 2 "two"
(integer) 1
redis> ZADD myzset 3 "three"
(integer) 1
redis> ZCARD myzset
(integer) 3
redis> ZCOUNT myzset 1 2
(integer) 2
````

"ZRANGE" gets the elements in a sorted set between certain range. "ZRANK" determine the rank of an element in the sorted set based on its score ordered from low to high.  "ZREVRANK" will do the same but ordered from high to low. "ZRANGEBYSCORE" is used to get elements in the sorted set with score between a certain range.

````
redis> ZRANGE myzset 2 3
1) "value"
redis> ZRANK myzset "value"
(integer) 2
redis> ZRANGEBYSCORE myzset 3 5
1) "one"
2) "two"
````

"ZREMRANGEBYRANK,ZREMRANGEBYSCORE" remove elements from the sorted set based on either the range of score or rank.

````
redis> ZREMRANGEBYRANK myzset 0 1
(integer) 2
redis> ZREMRANGEBYSCORE myzset 1 2
(integer) 1
````

"ZINTERSTORE,ZUNIONSTORE" are used to intersect or union two sorted sets. 

#### Bitmaps Commands

"GETBIT & SETBIT" are used to get or set a bit value at a certain offset in the string value stored at the key. "BITCOUNT" returns the number of bits in a string.

````
redis> SETBIT mykey 7 0
(integer) 1
redis> GETBIT mykey 0
(integer) 0
redis> SET mykey "foobar"
OK
redis> BITCOUNT mykey
(integer) 26
````

"BITOP" is used to perform a bitwise operation between multiple keys and store the result in a destination key. Operations are AND, OR, XOR and NOT.

````
redis> BITOP AND dest key1 key2
(integer) 6
````

#### HyperLogLog Commands

There are only 3 commands for this data type as explained below:

"PFADD" is used to add elements to the Hyperloglog structure.

````
redis> PFADD hll a b c d e f g
(integer) 1
````

"PFCOUNT" is used to count how many elements are inside the Hyperloglog

````
redis> PFADD hll foo bar zap
(integer) 1
redis> PFCOUNT hll
(integer) 3
````

"PFMERGE" is used mainly to merge multiple Hyperloglog together into a unique Hyperloglog and eliminate any duplicates. The result is stored then into a destination Hyperloglog

````
redis> PFADD hll1 foo bar zap a
(integer) 1
redis> PFADD hll2 a b c foo
(integer) 1
redis> PFMERGE hll3 hll1 hll2
OK
redis> PFCOUNT hll3
(integer) 6
````
