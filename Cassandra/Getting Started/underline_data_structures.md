#### [back](getting_started_main.md)


Cassandra bases its data distribution design on Amazon's Dynamo and its data model on Google's Bigtable. In this section I will explain Cassandra's main concepts related to its data model and data distribution designs.


##### Node

This is the basic infrastructure component used in Cassandra where you store your data. 

##### Data Center

A data centre in Cassandra is basically a group of related nodes. Different data centres are usually used to serve different workloads to prevent transactions from being impacted by other unrelated transactions. The replication configuration in Cassandra can be set on the data centre level or we can replicate the data across multiple data centres based on the replication factor. An example is if you have two data centres one to serve customers on the east side of the country "DC-EAST" and one to serve customers in the west side of the country "DC-WEST", but still they both share the same dataset.


##### Cluster

A cluster in cassandra is the outermost container and can contain multiple data centres that contain multiple nodes or machines. You can't do replicate the data across clusters.

##### A peer-to-peer Gossip protocol
Cassandra uses a peer-to-peer Gossip protocol that can be used by each node to discover the information and the state of the other nodes in the cluster. The protocol is a gossip protocol since the information is transferred from node to node in the cluster in a "Gossip" manner where nodes are transferring information that they know about any other nodes to their neighbours until all nodes knows about all other nodes in the cluster. This gossip information is retained and persisted in each node so that it can be used again once it restarts.
##### A partitioner 
When you write the data to any node in a Cassandra cluster, it will be automatically sharded and distributed across all the nodes. Cassandra uses a partitioner to determine how to distribute the data across the nodes. Cassandra support multiple partitioners that can be used such as the Murmur3Partitioner. The Murmur3Partitioner uses a hash function (MurmurHash) that can be used to uniformly distribute the data across the cluster and it the default partitioner used by Cassandra since it provides faster hashing and improves the performance. So basically, the partityioners are hash functions used to compute the tokens (hashing the partition key) and later these tokens are used to distribute the data as well as read/write requests uniformally across all the nodes in the cluster based on the hash ranges (Consistent hashing). 
##### The replication factorWhen you configure Cassandra, you need to set a replication factor which is used to determine the number of copies that should be kept for each written data. The replication factor is set in the cluster level and the data will be replicated to equally important replicas (not like in a master-slave replication). Generally, you should make the replication factor greater than one but not more than the number of nodes in the cluster. For example, if you set the replication factor to 3, there will be 3 copies stored in 3 different nodes for each data written. 
##### Replication placement strategy

Cassandra uses a replication strategy to determine which nodes to choose for replicating the data. There are different replication strategies that can be configured in Cassandra such as the SimpleStrategy or the NetworkTopologyStrategy. The SimpleStrategy  is the simplest strategy where it picks the first node in the ring to be the first replica then the second node will be the next node in the ring and so forth.  The NetworkTopologyStrategy is the recommended strategy since as the name indicates, it is aware of the network topology (location of the nodes in the data centres and racks). This means it is more intelligent than the simple strategy and it will try to choose different nodes in different racks for distribution which reduces the failures because usually the nodes in the same rack tends to fail together. 


##### Snitch

Cassandra uses a snitch to listen to all the machines in a cluster and monitor nodes and network performance. Then it can provide this information about the state of the nodes to the replication strategy to decide the best nodes that can be used for replicating the data. Cassandra supports different type of snitches such as the dynamic snitch which is enabled by default. Dynamic snitch monitors the performance of the different nodes such as the read latency and then use this information to decide where to replicate and from which replica to read the data. For example if a read request has been received to one of the nodes that doesn't have the data, then this node needs to route this request to the nodes that are having the data. The node then will use the dynamic snitch information to decide to which replica node it should forward the read request.



##### CQL 

Cassandra uses a SQL-like query language called CQL which stands for Cassandra Query Language that can be used to communicate with the Cassandra database. Additionally, Cassandra provides a CQL shell called cqlsh that can be used to interact with CQL. Cassandra supports also a graphical tool called Datastax DevCenter and many drivers for different programming languages to programatically interact with CQL.


##### Keyspace

Cassandra uses containers called Keyspaces which can be used to store multiple CQL tables and is closely compared to a database schema in relational databases. A good practice is to create one keyspace per application but it is also fine to create multiple keyspace per application. A Keyspace can be assigned a name and a set of attributes to define its behaviour. An example of the attributes that can be set for a Keyspace is the replication factor and the replication strategy. 


##### A CQL table
In Cassandra, the data is stored as columns that are accessible through a primary row key. The collection of multiple ordered columns that are having a primary row key is called a CQL table. 
##### Supported data types
CQL supports many built-in data types including a support for various types of collections such as maps, lists, and sets.  Below are a list of the most used data types:
<table border="1" cellpadding="4" cellspacing="0" class="table" frame="border" id="concept_ds_wbk_zdt_xj__table_cf5_bqt_xj" rules="all" summary="">

<thead align="left" class="thead">

<tr class="row">

<th class="entry" id="d7530e36" valign="top" width="25%">CQL Type</th>

<th class="entry" id="d7530e39" valign="top" width="25%">Constants</th>

<th class="entry" id="d7530e42" valign="top" width="50%">Description</th>

</tr>

</thead>

<tbody class="tbody">

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">ascii</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">strings</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">US-ASCII character string</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">bigint</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">64-bit signed long</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">blob</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">blobs</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Arbitrary bytes </td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">boolean</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">booleans</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">true or false</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">counter</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Distributed counter value (64-bit long)</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">decimal</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers, floats</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Variable-precision decimal

Java type


</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">double</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers, floats</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">64-bit IEEE-754 floating point

Java type

</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">float</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers, floats</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">32-bit IEEE-754 floating point

Java type

</td>

</tr>


<td class="entry" headers="d7530e36 " valign="top" width="25%">inet</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">strings</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">IP address string in IPv4 or IPv6 format</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">int</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">32-bit signed integer</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">list</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">n/a</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">A collection of one or more ordered elements</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">map</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">n/a</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">A JSON-style array of literals: { literal : literal, literal : literal ... }</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">set</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">n/a</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">A collection of one or more elements</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">text</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">strings</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">UTF-8 encoded string</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">timestamp</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers, strings</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Date plus time, encoded as 8 bytes since epoch</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">timeuuid</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">uuids</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Type 1 UUID only</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">tuple</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">n/a</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%"> A group of 2-3 fields.</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">uuid</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">uuids</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">A UUID</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">varchar</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">strings</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">UTF-8 encoded string</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">varint</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">integers</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">Arbitrary-precision integer

Java type

</td>

</tr>

</tbody>

</table>

##### Writing/reading path 

The data in Cassandra is first written to a commit log in the nodes, then the data will be captured and stored in a mem-table. Later when the mem-table is full, it will be flushed and stored in the SStable data file for durability. In the same time, all data will be replicated across all replicas in the cluster. A read operation can be done from any of the nodes, where it will first check if the data exists in the mem-table , else it will be routed to the appropriate SStable that holds the requested data. Below I will show the steps followed in Cassandra whenever a data is written:

1- First data will be appended to the commit log which will be persisted in disk and used later to recover data whenever the node crashed or shut down. The data in the commit log will be purged after the corresponding data in memtable is written to the SStable. 

2- The data will be written to the MemTable which is a write back cache that Cassandra uses to look up for data before looking into the disk. The data in the MemTable will be removed whenever the MemTable reach a configurable size limit.

3- Whenever the MemTable is full and reached the size limit, the data will be flushed from the MemTable to the SSTable. Once the data is flushed from the MemTable, the corresponding data in the commit log will be also removed.

4- Then the data will be stored in the SSTable. The SSTable is immutable which means it can't be overwritten. If a row column values are updated, then a new row with another timestamp version will be stored in the SSTable. The SSTable contains a partition index to be used in mapping partition keys with the row data in the SSTable files and a bloom filter which is a structure stored in memory than can be used to check if the row data exists in the MemTable or not before searching for it in the SSTable. 


The below figure shows the write path in Cassandra:

![image](https://docs.datastax.com/en/cassandra/2.0/cassandra/images/dml_write-process_12.png) 


The data is deleted in Cassandra by adding a flag to the data to be deleted in the SSTable. The flag called a tombstone and once a data is marked with a tombstone, it will be deleted after a configurable time called gc_grace_seconds. The data is removed during the compaction process which will be explained in the following section.


##### Compaction 


The written data in Cassandra aren't inserted or updated in place because the data in the SSTable can't be overwritten since the SSTable is immutable. Hence, Cassandra inserts a new timestamped version of the data whenever an insert or update occurs. The new timestamped version of the data will be kept in another SSTable. Additionally, the deleted data will not happen in place but instead the data is marked with a tombstone flag to be deleted after the the pre-configured gc_grace_seconds value. Because of this, after sometime there will be so many versions available for the same row in different SSTables. Therefore, Cassandra runs periodically a process called Compaction that will merges all the different versions of the data by selecting the latest version of the data based on the timestamps and create a new SSTable. The merging is done by the partition key and the data marked for deletion will be purged from the new SSTable. The old SSTable files will be deleted as soon as the process is completed and once the final merged SSTable is available. The compaction process is show in the below figure:


![image](https://docs.datastax.com/en/cassandra/2.0/cassandra/images/dml_compaction.png)










