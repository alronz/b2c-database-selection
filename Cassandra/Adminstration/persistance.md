#### [back](admin_main.md)


Cassandra is fully durable data store since the data will be written directly to a commit log once received. The commit log is persistence and is used in as a crash recovery mechanism whenever the node accidentally failed. The write will be considered successful only if it has been written to the commit log. After the write request is written to the commit log, then it will be written to the MemTable. The written data will be kept in the MemTable until it reaches a pre-configured size threshold. Then once this threshold is reached, data will be flushed to disk or what is called the SSTable files and be persisted permanently.  






