
#### [back](basic_features_main.md)

Redis provides a partial transactions support and can even provide almost full transactions when used with its built-in persistencey feature as will be explained [later](../Adminstration/persistance.md).  

Redis is a single threaded application which means that when a command is executed, it will be always the only one. So the idea behind transactions support in Redis is to wrap the transaction commands and execute them all as a single command. This is done by using MULTI/EXEC commands, you start the transaction by using MULTI command, then all the following commands will be queued. After all transaction commands are queued, they will be executed all as a single command using the EXEC command. The transaction commands will be executed sequentially and it will never happen that another command executed in the middle.

The transaction commands will be either executed together or none will be executed. This  provides us with an atomic transaction. This means that if the server crashes or the connection lost during executing the commands, none will be executed. However the problem occurs when any one of the transaction commands are mistakenly executed (not syntax mistakes) due to some programming bug. In this case unfortunately Redis doesn't provide any way for a Rollback as in traditional databases. Rollback isn't supported due to reasons related to performance.  

DISCARD can be used to abort a transaction before running EXEC. Example of using MULTI/EXEC is shown below:  

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

To avoid race conditions that will happen when another client tries to modify the same value used in a transaction, optimistic locking is provided using WATCH/UNWATCH commands. WATCH can be used to monitor a certain key during the transaction. If the value is modified during the transaction, you get notified then you can abort the transaction and try again. You can either unwatch a certain key or flush all watched keys by using UNWATCH command. Example is given below:

````
        try {
			this.jedisClient.watch("inventory:" + sellerID);
			if (!this.jedisClient.sismember("inventory:" + sellerID, sku)) {
				// inventory doesn't have this item
				this.jedisClient.unwatch();
				tryAgain();
			}

			Transaction transaction = this.jedisClient.multi();

			responses.add(transaction.srem("inventory:" + sellerID, sku));
			responses.add(transaction.sadd("bought:" + token, sku));

			transaction.exec();

			return checkReponses(responses);

		} catch (Exception e) {
		// race condition
		tryAgain();
		}
````

Finally, Redis support scripting using LUA which is transactional by definition since it can be executed as a single command. Hence Lua scripting can be used to support transactions. More about Redis scripting will be given [later](../Special Features/scripting.md).
