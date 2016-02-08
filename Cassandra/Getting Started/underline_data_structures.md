#### [back](getting_started_main.md)



Cassandra bases its data distribution design on Amazon's Dynamo and its data model on Google's Bigtable. In this section I will explain the main concepts in Cassandra related to its data model and data distribution designs.


#### Data distribution components

In this section, I will explain the main architectural components in Cassandra.

##### Node

This is the basic infrastructure component used in Cassandra where you store your data. 

##### Data Center

A data centre in Cassandra is basically a group of related nodes. Different data centres are usually used to serve different workloads to prevent transactions from being impacted by other unrelated transactions. The replication configuration in Cassandra is set on the data centre level and we can replicate the data across multiple data centres based on the replication factor. It is always a good practice not to use data centres that span multiple psychical locations.


##### Cluster

A cluster in cassandra is the outermost container and can contain multiple data centres  that contain multiple nodes or machines. 

##### A peer-to-peer Gossip protocol
Cassandra uses a a peer-to-peer Gossip protocol to discover information and state of other nodes in the cluster. The protocol is a Gossip protocol since the information is transferred from node to node in the cluster in a Gossip fashion where nodes are transferring information that it knows about to its neighbours until all nodes knows about all other nodes in the cluster. This Gossip information is retained and persisted in each node so that we can use it again when a node restarts.
##### A partitioner 
When you write the data to any node in a Cassandra cluster, it will be automatically distributed across all the nodes. Cassandra uses a partitioner to determine how to distribute the data across the nodes. Cassandra support multiple partitioners that can be used such as the Murmur3Partitioner which is the default partitioner used by Cassandra. The Murmur3Partitioner uses a hash function to uniformly distribute the data across the cluster and it the default partitioner used by cassandra since it provides faster hashing and can improve performance.
##### The replication factorWhen you configure Cassandra, you need to set a replication factor which is used to determine the number of copies that should be kept for each written data. The replication factor is set in the cluster level and the data will be replicated in an equally important replicas (not like in a master-slave configuration). Generally, you should make the replication factor greater than one but not more than the number of nodes in the cluster.
##### Replication placement strategy

Cassandra uses a replication strategy to determine in which nodes it should replicate the data to and where to store the first copy of the data. There are different replication strategies used such as the SimpleStrategy or the NetworkTopologyStrategy.


##### Snitch

Cassandra uses a snitch to listen to all the machines in a cluster and monitor performance to give this information for the replication strategy to decide the best nodes that can be used to replicate the data to. A dynamic snitch is enabled by default to monitor and analysis all the nodes in the cluster and provide information regarding the nodes state and performance.



##### CQL 

Cassandra uses a sql-like query language called CQL which stands for Cassandra Query Language that can be used to communicate with the cassandra database. Cassandra provides also a CQL shell called cqlsh that can be used to interact with CQL. Cassandra supports also a graphical tool called Datastax DevCenter as well as a support for many drivers where you can execute CQL statements through them.



##### Keyspace

Cassandra uses containers called Keyspaces which is can be used to store multiple tables and is closely corresponding to a database in a relational database. A good practice is to create one keyspace per application but it is also fine to create more than one keyspace per application. A keyspace can be assigned a name and a set of attributes to define its behaviour. An example of the attributes that can be set for a keyspace is the replication factor and the replication strategy. 


##### A CQL table
In cassandra, the data is stored as columns and fetched by a row key. The collection of order columns that are having a primary row key is called a table. 
##### Supported data types
CQL supports many built-in data types including a support for various types of collections such as maps, lists, and sets.  A list 
<table border="1" cellpadding="4" cellspacing="0" class="table" frame="border" id="concept_ds_wbk_zdt_xj__table_cf5_bqt_xj" rules="all" summary=""><caption><span class="tablecap">CQL Data Types</span></caption>

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

<td class="entry" headers="d7530e42 " valign="top" width="50%">Arbitrary bytes (no validation), expressed as hexadecimal</td>

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

<div class="note note"><span class="notetitle">Note:</span> When dealing with currency, it is a best practice to have a currency class that serializes to and from an int or use the Decimal form.</div>

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

<td class="entry" headers="d7530e42 " valign="top" width="50%">IP address string in IPv4 or IPv6 format, used by the python-cql driver and CQL native protocols</td>

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

<td class="entry" headers="d7530e42 " valign="top" width="50%">Cassandra 2.1 and later. A group of 2-3 fields.</td>

</tr>

<tr class="row">

<td class="entry" headers="d7530e36 " valign="top" width="25%">uuid</td>

<td class="entry" headers="d7530e39 " valign="top" width="25%">uuids</td>

<td class="entry" headers="d7530e42 " valign="top" width="50%">A UUID in [standard UUID](http://en.wikipedia.org/wiki/Universally_unique_identifier) format</td>

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

The data in cassandra are first written to a commit log in the nodes, then the data will be captured and stored in a mem-table. Later when the mem-table is full, it will be flushed and stored in the SStable data file for durability. In the same time, all data will be replicated across all replicas in the cluster. A read operation can be done from any of the nodes, where it will first checks the data from the mem-table , else it will routed to the appropriate SStable that holds the requested data. Below I will show the steps followed in Cassandra whenever a data is written:

1- First data will be appended to the commit log which will be persisted in disk and used later to recover data in node whenever the node crashed and restarted. The data in the commit log will be purged after the corresponding data in memtable is written to the SStable. 

2- The data will be written to the MemTable which a write back cache that Cassandra uses to look up for data before looking into the disk. The data in the MemTable will be removed whenever the MemTable reach a configurable size limit.

3- Whenever the MemTable is full and reached the size limit, the data will be flushed from the MemTable to the SSTable. Once the data is flushed from the MemTable, the corresponding data in the commit log will be also removed.

4- The data will be stored in the SSTable. The SSTable is immutable which means it can't be overwritten. If a row column values are updated, then a new row with another timestamped version will be stored in the SSTable. The SSTable contains a partition index to be used in mapping partition keys with the row data in the SSTable files and a bloom filter which is a structure stored in memory which is used to check if the row data exists in the MemTable or not before searching for it in the SSTable. 


The below figure shows the write path in Cassandra:

![image](https://docs.datastax.com/en/cassandra/2.0/cassandra/images/dml_write-process_12.png) 


The data is deleted in Cassandra by adding a flag to the data to be deleted in the SSTable. The flag called a tombstone and once a data is marked with a tombstone, it will be deleted after a configurable time called gc_grace_seconds. The data is removed during the compaction process which will be explained in the following section.


##### Compaction 


Because the data in Cassandra doesn't do an inert or update in place which means that the data in the SSTable cann't be changed or overwritten since the SSTable are immutable. Cassandra writes a new timestamped version of the data whenever an insert or update occurs. The new timestamped version of the data will be kept in another SSTable. In addition, a data delete will not happen in place. However, the data is marked with a tombstone flag and will be deleted after the the pre-configured gc_grace_seconds value.  After sometime, there will be many versions available for the same row in different SSTables. Therefore, Cassandra runs periodically a process called Compaction that will merges all the different versions of the data by selecting the latest version of the data and create a new SSTable. The merging is done by the partition key and the data marked for deletion will be purged from the new SSTable. The old SSTable files will be deleted as soon as the process is completed and once the final merged SSTable is available. The compaction process is show in the below figure:


![image](https://docs.datastax.com/en/cassandra/2.0/cassandra/images/dml_compaction.png)










