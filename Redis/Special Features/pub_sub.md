This is a special feature of Redis that can be used to implement many applications. The idea is mainly characterized by listeners who can subscribe to cetain channels to recieve real time messages. The publishers can send binary messages of type string to channels without having any knoweldge of what if any subcribers are ther. This feature is following the publish/subscribe messaging paradigm. For example if the subsciber issue the below command:

````
SUBSCRIBE channel1 channel2
````

Then Redis will push any message that was published by any client to these channels to all the subscribed clients.  The subscriber can unsubscribe from the channels whenever he wants by issueing below command:

````
UNSUBSCRIBE channel1 channel2
````

Publishing the messages can be done by issueing the below command:

````
PUBLISH channel1 Hello
````


Another interesting command is the pattern-matching subscribing or publishing. An example is given below:


````
PSUBSCRIBE news.* 
````

This command will publish messages to all the channels that start with the word "news." such as news.weather or news.sport. The same can be used for subscription as seen below:

````
PSUBSCRIBE news.*
# or
PUNSUBSCRIBE news.*
````

This feature is very useful and can be used to implement many applications such as a chat server and many others,
