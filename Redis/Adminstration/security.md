#### [back](admin_main.md)

Securing your Redis server from external threats is so important since if your server is exposed to the outsider world, a single command like FLUSHALL can delete all the data you have. This is why your Redis server shouldn't be exposed to the outside world and unauthorised requests should be denied. There should be always a layer between Redis server and the clients. For example a web application layer that provides ACLs and validating users and deciding which actions to be performed against Redis instance. Your Redis server can be also configured to be bind to only one IP using the below command:

````
bind 127.0.0.1
````

So as mentioned, Redis doesn't provide any access control and this should be provided by the a separate authorisation layer. However Redis provides an authentication mechanism that is optional and can be turned on from redis.conf. A client can send an AUTH command followed by a password which will be checked against the user's password that was set in the configuration file.

Redis doesn't also support any encryption mechanism and this should be also implemented using a separate layer like by using encryption mechanisms such as SSL proxy.

A nice way of providing a security defence measures for your database is by using the RENAME command provided by Redis. Using this command you can simply rename the original commands names which reduces the risk that might happen if unauthorised clients accessed your server. An example of using this command is shown below:


````
rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
````

As shown above, now CONIG command is renamed to an impossible to guess name. In addition, NoSQL injection attacks are impossible since Redis protocol has no concept of string escaping.