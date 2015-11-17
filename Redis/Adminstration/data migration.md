Sometimes we want to migrate our old data or insert a large amount of preexistence data into our Redis server. Inserting a millions of keys of data to Redis at short amount of time and without blocking the server operations for other clients is important topic. Luckily Redis supports a pipe mode that was designed in order to perform mass insertion. Using this utility is so simple, you just need to put all the data you want to insert in a file and then performs the below command:

````
cat data.txt | redis-cli --pipe
````

This will produce an output similar to the below:


````
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 1000000
````

So you can know the result of the mass insertion and whether there was errors or not.