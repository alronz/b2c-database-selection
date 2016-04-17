

### [Neo4j](../Neo4j.md) > [Administration and Maintenance](Administration and Maintenance.md) > Persistency
___


Neo4j is durable and all written data is persisted on disk. As we already know by now, all data writes in Neo4j are handled as transactions and whenever a transaction is marked as successful and committed, it will be written directly to disk. 