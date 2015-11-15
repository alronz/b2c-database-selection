Piplining is supported with Redis by batching multiple commands and send them together to the server without waiting for their replies, and at the end they read the replies in a single step. Piplining will have great impact on system performance. Piplining is either provided using the redis clients that are supported for different languages as shown below using Ruby client or by using Redis scripting that will be explained [later](link to redis scripting).

````
pipe = conn.pipeline(False) // false means without transaction support (no MULTI/EXEC)pipe.zadd('viewed:' + token, item, timestamp)pipe.zremrangebyrank('viewed:' + token, 0, -26)pipe.zincrby('viewed:', item, -1)pipe.execute()````
