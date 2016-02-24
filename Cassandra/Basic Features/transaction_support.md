#### [back](basic_features_main.md)


Before start discussing how transactions are supported in Cassandra, I will first explain briefly how Cassandra supports the ACID (Atomicity, Consistency, Isolation, Durability) properties.

##### Consistency

Data Consistency in Cassandra means how the data is kept up-to-date between all the replica nodes. Cassandra supports a configurable consistency levels or what is called a tunable consistency. Additionally, Cassandra supports a linearisable consistency that can be used in transactions as will be explained in the following sections.   

###### Tunable consistency

In Cassandra, you can configure the read and write consistency levels to provide strong or eventual consistency depending on your application requirements. Depending on the configured consistency level, Cassandra can be a CP system (consistent and partition tolerant) or AP system (highly available and partition tolerant) in terms of the CAP theorem. The consistency level can be configured to determine how many replicas need to acknowledge the read or the write operation before considering the operation as a successful or as a failure.  A common practice is to configure the consistency level to be of a QUORUM which means the majority number of replica should acknowledge the request before it will be considered as successful. In general the consistency level should be less than the replication factor. To measure how strong is the consistency level, the below two equations can be used:

To get a strong consistency, the below inequality should be satisfied:

R+W > N

Where R is the consistency level of a read request and W is the consistency level of a write request and the N is the number of replicas. In the same way, we have eventual consistency when the below inequality is satisfied:

R + W =< N


###### Linearizable consistency

This consistency level is used when we want to achieve a serial isolation level. We need this  serial isolation level in applications where we need to perform a sequence of isolated operations where each operation must not be interrupted by others. Cassandra uses this consistency level when it needs to execute Lightweight transactions as will be explained later.


##### Atomicity

At the row level or the partition-level, the insert, update, and delete operations are all executed atomically depending on the consistency level configured. This means if you configured the write consistency level to 2 and the replication factor is 3, then any write operation will be considered a successful only when we get acknowledgements from at least 2 replicas, otherwise it will be considered a failure. 


##### Isolation

Cassandra provides full isolation at the low-level which means that any writes from a client application will not be visible to other until it is completed. 


##### Durability

Cassandra is fully durable and all writes will be persisted on disk since nodes are using a persisted commit log and the write is considered a successful only after it is written to the commit log. If the node crashes, the commit log will be played back once the node restarts and it will recover the data written before the crash.


##### Transactions

Cassandra is a distributed system with no single point of failure which means that we don't have a single master that handles all writes. This means we can't simply write some locks in the master node to implement transactions. Therefore, Cassandra uses a transaction mechanism called lightweight transactions which are implemented using the Paxos protocol. Paxos is a well-known consensus protocol that is used in distributed systems to make all nodes agree on proposals based on the majority voting. Paxos ensures that there exists only one agreed values between all the nodes which is what we need to implement transactions. Using this approach, Cassandra provides the ability to implement transactions. However this comes with a high cost since there will be many round trips between nodes in a cluster and should be used only whenever it is really necessary.  Using lightweight transactions will allow us to have a lineaizable consistency level suitable for transactions. 

An example of how to use the lightweight transactions is if we want to create an order in a B2C application which will decrease the inventory by one. Then we need to do it in a transaction to prevent any concurrent write operations that tries to decrease the inventory at the same time. We can do that using the lightweight transaction "IF" statement as shown below:

First we check what is the current quantity of the particular product:

````
SELECT quantity FROM products WHERE id = 1;
````

Then to decrease this quantity during the order, we do it as below:

````
UPDATE products SET quantity = existing_quantity -1
WHERE id = 1
IF quantity = existing_quantity;
````

Then if we want to do a complete transaction, we will create an order only after the decreasing of the quantity is a successful as shown below:


````
UPDATE products SET quantity = 9
WHERE id = 1
IF quantity = 10;

if(aboveQuerySuccessful())
  insertNewOrder();
else
 retry();
````


Another example if we want to create a customer account and make sure that the user doesn't already exist, then we do it as shown below:

````
INSERT INTO CUSTOMERS (user, email, name)
VALUES ('John', 'user@email.com', 'John Robert', 1)
IF NOT EXISTS;
````


As seen above, unlike in relational database transactions, Cassandra lightweight transactions uses optimistic locking instead of pessimistic locking and doesn't have the common BEGIN/COMMIT/ROLLBACK statements. In Cassandra lightweight transactions, we can only ensure that a given sequence of operations are executed as a single unit.

