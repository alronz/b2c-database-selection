[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Administration and Maintenance > Security


In this section I will talk about what are the security measures that MongoDB provides in order to ensure that the data is securely stored. MongoDB offers many security features such as Authentication, Role-Based access control, and communication encryption. In the following sections, I will talk about these features:


##### Authentication


MongoDB supports a way to verify the identity of clients who are trying to connect to the database. To authenticate the user, the db.auth() method can be used as shown below:

````
db.auth( <username>, <password> )
````

This method will return 1 when authentication is successful or 0 if the authentication failed.


##### Role-Based access control

MongoDB supports a Role-Based Access Control (RBAC) that can be used to access the database. Each user can be granted a set of roles that determine the user access permissions to the database resources. To enable the role based access control, you need to use the "--auth" option using mongod or setting the "security.authorization " to "enabled" in the configuration file. 

Each user can be granted roles which are a set of privileges to perform some actions on specific resources. A resource is either a database, a collection, set of collection or a cluster. The action is the operation that is allowed on a certain resource. The roles are assigned to the user during user creation.


##### Communication encryption

MongoDB supports the use of TLS/SSL protocols for encrypting the connections to mongod or mongos instances. 


