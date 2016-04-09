[Home](../../index.md)

[Neo4j Content](../Neo4j.md)
___

# Neo4j > Administration and Maintenance > Security


Neo4j supports different security options on the data level as well as on the server level. In this section, I will talk about the security options available in Neo4j.



##### Data Security

Neo4j support all the security methods available in the Java programming language and the JVM to encrypt the data before storing for protection from any unauthorised access. Security isn't handled automatically and needs to be implemented in the upper layers. 


##### Server Security

The server instance replies only to requests coming from the localhost and port 7474 to protect the server from malicious requests. To change this setting, you can set the configuration parameter below:

````
org.neo4j.server.webserver.address=0.0.0.0
// Or 
org.neo4j.server.webserver.port=7474
````

In addition, Neo4j requires clients to supply some authentication credentials when trying to call the REST API. The data related to authentication and authorisation is usually stored in the data/dbms/auth folder. It is even possible to disable the authentication and authorisation if it is required in some use cases as seen below:

````
dbms.security.auth_enabled=false
````

It is also possible to use SSL secured communication over HTTPS. To provide the key and certificate that will be used during the SSL communication, you need to use the settings below:

````
dbms.security.tls_certificate_file=ssl/snakeoil.cert

dbms.security.tls_key_file=ssl/snakeoil.key
````


To use more advanced security options such as using custom security rules or other security proxy server, please read the [documentations](http://neo4j.com/docs/stable/security-server.html).