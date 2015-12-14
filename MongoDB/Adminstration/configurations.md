As we already mentioned in the installation section, Redis can start without creating a configuration file and then it will take its built-in default configurations. For production environment you will need to choose your own configuration paramers depending on your settings. The Redis configuration is called redis.confi and will contain configuration with the below format:

````
confugirationKeyWord argument1 argument2 ... argumentN
````

You can also pass configuration parameters via the command line which is very useful for development and testing environment. Example is shown below:

````
./redis-server --port 6380 --slaveof 127.0.0.1 6379
````

Finally configuring Redis while the server is running is also possible without the need to stop or restart the server. This is supported using the commands CONFIG GET , CONFIG SET which support most of the configuration keywords. Example is shown below:

````
CONFIG SET SAVE "900 1 300 10"
````

Above command will change the configuration related to persistence which will be explained later.

I am going to explain in the following sections how to configure Redis for various administration and maintenance tasks such as configuring security , scalability and upgrade or backup. For a complete list of all the possible configurations that you can use in Redis, please check Redis [documentation](https://raw.githubusercontent.com/antirez/redis/3.0/redis.conf).
