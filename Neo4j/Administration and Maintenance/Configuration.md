

### [Neo4j](../Neo4j.md) > [Administration and Maintenance](Administration and Maintenance.md) > Configuration
___


A Neo4j database server instance can be started without providing a configuration file and then the default configuration options will be considered. If you want to change the server configuration options, you need to change the settings inside the configuration file conf/neo4j-server.properties. In this section, I will talk about some of the main configuration options that you might want to change. 


If you want to change the location of the database file that will contain your database changes, you can change the below configuration parameter:

````
org.neo4j.server.database.location=data/graph.db
````


The URI path of the HTTP REST API can be changed using the below configuration option:

````
org.neo4j.server.webadmin.data.uri=/db/data/
````

Also you can change the transaction timeout, which is the time used by the Neo4j instance to wait for the transaction to commit. After the timeout, the server will perform a roll back for the transaction. This configuration option can be changed as shown below:


````
org.neo4j.server.transaction.timeout=60
````


The logging configurations can be changed using the below configuration parameters:

````
org.neo4j.server.http.log.enabled=true
org.neo4j.server.http.log.config=conf/neo4j-http-logging.xml
````

The first option enable or disable logging and the second option specify the logging file. 

There are many other configuration options that can be used to configure performance, security and other operating features. For a full list of all the configuration options, please check out the [documentations](http://neo4j.com/docs/stable/configuration-settings.html).