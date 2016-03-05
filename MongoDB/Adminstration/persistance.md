#### [back](../admin_main.md)

MongoDB supports two persistent storage engines: the WiredTiger storage engine or the MMAPv1 storage engine. Another non-persistent storage engine is also supported which is called the In-Memory storage engine where all data will be lost if the mongod instance is shut down for any reason. The in-memory storage engine is still in beta and is mainly used to get more predictable latency of database operations. Usually, persistency is supported by flushing the data from the memory to disk periodically (default is each 60 seconds). In addition, a mechanism called journalling is also used to provide more durable solution in the event of failure. MonogoDB uses journal files to store the in-memory changes and can be used later to recover the data when the server crashes before flushing data to disk files. In the following sections, I will talk about how persistency is supported in the WiredTiger and MMAPv1 storage engines.


#### The WiredTiger storage engine

This is storage engine is the default engine used by MongoDB version 3.2. In this storage engine, MongoDB takes a point-on-time snapshots for the user's data each 60 seconds or when 2 GB of journal data has been written if journalling is enabled. Journalling is enabled by default and if not you can enable it by changing the "storage.journal.enabled" option to false. Then If a new checkpoint is successfully written, the old checkpoint will be freed. This means that the WiredTiger storage engine persists all the data modifications between checkpoints, however if the server exits between the checkpoints, it will use the journal files to recover the modifications since last checkpoint. 


#### The MMAPv1 storage engine

Using this storage engine, MongoDB first writes the changes to the private in-memory view when a write operation occurs. Then the private view changes will be written to the journal files. And then upon a journal commit, the data will be written to the shared view. The shared view flushes the data to the on-disk files periodically (usually each 60 seconds). So if the mongod instance crashed before flushing the shared view to disk, the journal files can be used to recover the shared view. If the journal files contain only already flushed data, then it can be recycled for reuse.


From the above, we can see that the write operations in MongoDB are durable when journalling is enabled. Otherwise, write operations could be lost between checkpoints or flushing data to disk. So the write operation is considered durable when it has been written to the journal files in case of a single server mongod. In case of a replica set, the write operation is durable if it has been written to the journal files of the majority voting nodes.









