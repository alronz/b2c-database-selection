
#### [back](basic_features_main.md)

Redis provides a partial transactions support and can even provide almost full transactions when used with its built-in persistance feature as will be explained [later](../Adminstration/persistance.md).  

Redis is a single threaded application which means that when a command is executed, it will be always the only one. So the idea behind transactions support in Redis is to wrap the transaction commands and execute them all  as a single command. This is done by using MULTI/EXEC commands, you start the transaction by using MULTI command, then all the following commands will be queued. After all transaction commands are queued, they will be executed all as a single command using the EXEC command. The transaction commands will be executed sequentially and it will never happen that another command issued by another client execute in the middle.

The transaction commands will be executed together or none will be executed which provides you with an atomic transaction. This means that if the server crashes or the connection lost during executing the commands, none will be executed. However the problem occurs when any one of the transaction commands are executed wrongly (not syntax error) due to some programming error. In this case unfortunately Redis will execute the rest of commands and ignore the errored ones and there is no way for a Rollback as in traditional databases. Rollback isn't supported due to reasons related to performance. Also since they only impacted commands are the ones wrong due to some programming bug which usually doesn't happen in a production environment. 

DISCARD can be used to abort transaction before running EXEC. Example of using MULTI/EXEC is shown below:  

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

To avoid race conditions when another client tries to modify the same value used in a transaction, optimistic locking is provided using WATCH/UNWATCH commands. WATCH can be used to monitor a certain key during the transaction. If the value is modified during the transaction, you get notified then you can abort the transaction and try again. You can either unwatch a certain key or flush all watched keys by using UNWATCH command. Example is given below:

````
pipe.watch(inventory)if not pipe.sismember(inventory, itemid):pipe.unwatch()pipe.multi()pipe.zadd("market:", item, price)pipe.srem(inventory, itemid)pipe.execute()return Trueexcept redis.exceptions.WatchError:pass
````

A Final note is that Redis support scripting using LUA which is transactional by definition since it can be executed as a single command. Hence Lua scripting can be used to support transactions. More about Redis scripting will be given [later](../Special Features/scripting.md).
