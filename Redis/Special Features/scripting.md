#### [back](special_features_main.md)

Redis allows you to execute Lua scripts inside the Redis server. Scripting Redis with Lua is useful and can be used to avoid some common pitfalls that slow down development or reduce performance. To load the script to Redis server you can use either EVAL or EVALSHA commands which will evaluate the scripts using the Lua interpreter and load it. By using EVAL you can simply load a Lua program without defining a Lue function and pass arguements to the Lua program as redis keys. Example is given below :

````
> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"
````

In order to call Redis commands from the Lua program you can use redis.call() as seen below :

````
> eval "return redis.call('set',KEYS[1],'bar')" 1 foo
OK
````

The good thing is the fact that there is a one-to-one conversion between Lua program data types and Redis data types. You can check Redis [documentation](http://redis.io/commands/eval) to see the conversion table.

An important note is that the script is executed in Atomicity way which makes it useful for transaction related tasks.

EVALSHA is also similar to the way EVAL works, the only differnce is that it takes as a first argument the SHA1 digest of the script and not the complete script. The idea behind this is to reduce the bandwidth. So if the Redis server still remembers a script with the matching SHA1 digest, it will retrieve and that script from cash and execute it. Else it will throw an error. 
Example is given below:

````
> evalsha 6b1bf486c81ceb7edf3c093f4c48582e38c0e791 0
"results"
````