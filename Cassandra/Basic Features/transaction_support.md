#### [back](basic_features_main.md)


Before start discussing how transactions are supported in Cassandra, I will first explain briefly how Cassandra supports the ACID (Atomicity, Consistency, Isolation, Durability) properties.

##### Consistency

Data Consistency in Cassandra means how the data is kept up-to-date between all the replica nodes. As Cassandra is a AP system in terms of the CAP theorem which means it supports high availability and partition. In addition, Cassandra supports a configurable consistency. You can either use a tunable consistency or a linearisable consistency as will be explained in the following sections.   

###### Tunable consistency

In Cassandra, you can configure your read and write consistency level to support a strong or eventual consistency depending on your application requirements. Depending on the consistency level configured, Cassandra can be a CP system (consistent and partition tolerant) or AP system (highly available and partition tolerant) in terms of the CAP theorem. The consistency level can be configured to determine how many replicas need to confirm the read or the write operation before considering the operation a success or a failure.  A common practice is to configure the consistency level to be of a QUORUM which means the majority number of replica should acknowledge the request before considered as successful. In general the consistency level should be less than the replication factor. To measure how strong is the consistency level, the below two equations can be used:

To get a strong consistency, the below inequality should be satisfied:

R+W > N

where R is the consistency level of a read request and W is the consistency level of a write request and the N is the number of replica. However, the week or eventual consistency is when the below inequality is satisfied:

R + W =< N


###### Linearizable consistency

This consistency level is used when we want to achieve a serial isolation level that is needed if we want to perform a sequence of operations where each operation must not be interrupted by others. Cassandra uses this consistency level when it needs to execute Lightweight transactions as will be explained later.


##### Atomicity

At the row level or the partition-level, the insert, update, and delete operations are all executed atomically depending on the consistency level configured. This means if you configured the write consistency to 2 and the replication factor is 3, then any write operation will be considered as successful only when it get acknowledgements from at least 2 replica, else it will be considered as failure. 


##### Isolation

Cassandra provides full isolation at the low-level which means that any writes from a client application will not be visible to other until it is completed. 


##### Durability

Cassandra is fully durable and all writes will be persisted on disk and since nodes are using a persisted commit log, any crash or server failure happens before the MemTable is flushed to disk will not be lost since the commit log will be played back to recover the data written before the crash once the node restarts.


##### Transactions

Cassandra is a distributed system with no single point of failure which means that we don't have a single master for write where we can implement locks easily to implement transactions. Cassandra uses a transaction mechanism called lightweight transactions which are implemented using Paxos. Paxos is a well-known consensus protocol that is used in distributed systems to make all nodes agrees on proposals based on the majority voting. Paxos ensures that there exists only one agreed values between all the nodes which is what we need to implement transactions. Using this approach, Cassandra provides the ability to implement transactions. However this comes with a high cost since there will be many round trips between nodes in a cluster and should be used only whenever it is really necessary.  Using lightweight transactions will allow us to have a lineaizable consistency level suitable for transactions. 

An example of how to use the Cassandra is if we want to create an order in a B2C application which will decrease the inventory by one, then we need to do it in a transaction to prevent any concurrent write operations that tries to decrease the inventory at the same time. We can do that using the lightweight transaction "IF" statement as shown below:

First we check what is the current quantity of the particular product:

````
SELECT * FROM products WHERE id = 1;
````
Then we will get the product quantity and to decrease this quantity during the order, we do it as below:

````
UPDATE products SET quantity = existing_quantity -1
WHERE id = 1
IF quantity = existing_quantity;
````

Another example if we want to create a customer account and make sure that the user doesn't already exist, then we do it as shown below:

````
INSERT INTO CUSTOMERS (user, email, name)
VALUES ('John', 'user@email.com', 'John Robert', 1)
IF NOT EXISTS;
````


As seen above, unlike relational databases transactions, Cassandra lightweight transactions uses optimistic locking instead of a pessimistic locking and doesn't have the common BEGIN/COMMIT/ROLLBACK statements used in transaction. In Cassandra lightweight transactions, we can only ensure that a given sequence of operations are executed as a single unit.

