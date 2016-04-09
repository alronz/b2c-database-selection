[Home](../../index.md)

[Cassandra Content](../Cassandra.md)
___

# Cassandra > Administration and Maintenance > Persistency

Cassandra is fully durable data store since the data will be written directly to a commit log once received. The commit log is then persisted to disk and later it can be used as a crash recovery mechanism whenever the node accidentally failed. The write operation will be considered a successful only once it has been written to the commit log. After the write operation is written to the commit log, it will be written to the MemTable. The written data will be kept in the MemTable until it reaches a pre-configured size threshold. Once this threshold is reached, data will be flushed to disk or what is called the SSTable files and be persisted permanently.  






