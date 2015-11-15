Redis provides a partial transactions support and can even provide almost a full transactions support if used with its built-in persistance feature as will be explained [later](link to persistance support).  

Redis is single threaded which means that when a command is executed, it will be the only command executing at that time. So the idea behind transactions support in Redis is to wrap the transactions commands and execute and execute them all in a single command. This is done by using MULTI/EXEC commands. When execute MULTI command, all the commands that will be executed after it will be queued until EXEC command is triggered where all of the queued commands will be executed at once. The transactions commands will be executed sequentially and it will never happen that another command issued by a differnt client will be executed in the middle. The commands will be also executed together or none will be exectued which provides you with atomic transaction. This means that if the server crashes or the connection lost during executing the commands, none will be executed. However the problem occur when any one of the transaction commands errored out during execution due to syntax  or any other errors. In this case unfortunately Redis will execute the rest of commands and ignore the errored ones. This is because Rollback is not supported as in traditional databases due to reasons related to performance. DISCARD can be used to to abort transaction before running EXEC. Example of using MULTI/EXEC is shown below:  

````
> MULTI
OK
> INCR foo
QUEUED
> DISCARD
OK
> MULTI
OK
> INCR foo
QUEUED
> EXEC
````

To avoid race conditions when another client tries to modify a value that is used by a transaction, optimistic locking is provided by using WATCH/UNWATCH commands. WATCH can be used to monitor a certain key during the transaction and when modifed we get notified and you can abort transaction and try again until you succeed. You can also either unwatch a certain key or flush all watched keys by using UNWATCH command. Example is given below:

````
pipe.watch(inventory)if not pipe.sismember(inventory, itemid):pipe.unwatch()pipe.multi()pipe.zadd("market:", item, price)pipe.srem(inventory, itemid)pipe.execute()return Trueexcept redis.exceptions.WatchError:pass
````

Final note is that Redis support scripting using LUA and it is transactional by definition, hence it can be used to support transactions. More about Redis scription will be given [later](link to Redis scripting).
