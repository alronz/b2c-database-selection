[Home](../../index.md)

[Cassandra Content](../Cassandra.md)
___

# Cassandra > Administration and Maintenance > Security


In the community version of Cassandra, you can provide security for your data by the following features:


##### SSL encryption

All the communications between the client and any of the database nodes are secured by SSL encryption. This ensures that the data is transferred in a secure way and won't be compromised. 


##### Authentication based control

Cassandra supports the creation of users and roles. Then any one who wants to connect to the database needs to be authenticated first. CQL supports the creation of the users and roles using the CREATE USER and the CREATE ROLE statements. The users and the roles can be dropped or altered later if needed. This provides security for the data since the users are authenticated first using passwords and can access the data based on pre-defined roles.


##### Object permission

Cassandra supports also object permissions that can be granted to the intended users only. The permission can be granted or revoked using CQL statements GRANT or REVOKE.  
