#### [back](special_features_main.md)

This is a special feature of Redis that can be used to implement many applications. The idea is mainly characterised by listeners who can subscribe to certain channels to receive real time messages. The publishers can send binary messages of type string to channels without having any knowledge of whether there are subscribers or not. This feature is following the publish/subscribe messaging paradigm. For example if a subscriber issue the below command:

````
SUBSCRIBE channel1 channel2
````

Then Redis will push any message that was published by any client to these channels to all the subscribed clients.  The subscriber can unsubscribe from the channels whenever he wants by issuing below command:

````
UNSUBSCRIBE channel1 channel2
````

Publishing the messages can be done by issuing the below command:

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

This feature is very useful and can be used to implement many applications such as a chat server.
