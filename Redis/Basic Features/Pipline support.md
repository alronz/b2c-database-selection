
#### [back](basic_features_main.md)

Sometimes we want to migrate our old data or insert a large amount of preexistence data into our Redis server. Inserting a millions of keys of data to Redis at short amount of time and without blocking the server operations is very important feature. Luckily Redis supports a pipe mode that was designed in order to perform mass insertion. Using this utility is so simple, you just need to put all the data you want to insert in a file and then performs the below command:

````
cat data.txt | redis-cli --pipe
````

This will produce an output similar to the below:


````
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 1000000
````


Pipelining is also supported in Redis which is used to batch multiple commands and send them together to the server at once without waiting for their replies. At the end you can read the replies in a single step. Pipelining can improve system performance since the round trips between client and server are reduced. Pipelining is either provided using the Redis clients as shown below or by using Redis scripting that will be explained [later](../Special Features/scripting.md).

````
pipe = conn.pipeline(False) // False means without transaction supportpipe.zadd('viewed:' + token, item, timestamp)pipe.zremrangebyrank('viewed:' + token, 0, -26)pipe.zincrby('viewed:', item, -1)pipe.execute()````



