#### [back](admin_main.md)


Neo4j is durable and all your written data is persisted to disk. As we already know by now, all data writes in Neo4j are handled as transactions and whenever a transaction is marked as successful and committed, it will be written directly to disk. 