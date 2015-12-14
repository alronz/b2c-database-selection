
#### [back](basic_features_main.md)

Pipelining is supported in Redis by batching multiple commands and send them together to the server at once without waiting for their replies, then at the end you can read the replies in a single step. Pipelining can improve system performance a lot since the round trips between client and server are reduced. Pipelining is either provided using the Redis clients as shown below using Ruby client or by using Redis scripting that will be explained [later](../Special Features/scripting.md).

````
pipe = conn.pipeline(False) // False means without transaction supportpipe.zadd('viewed:' + token, item, timestamp)pipe.zremrangebyrank('viewed:' + token, 0, -26)pipe.zincrby('viewed:', item, -1)pipe.execute()````
