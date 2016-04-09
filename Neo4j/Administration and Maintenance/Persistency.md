[Home](../../index.md)

[Neo4j Content](../Neo4j.md)
___

# Neo4j > Administration and Maintenance > Persistency


Neo4j is durable and all written data is persisted on disk. As we already know by now, all data writes in Neo4j are handled as transactions and whenever a transaction is marked as successful and committed, it will be written directly to disk. 