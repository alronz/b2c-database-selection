

### [Cassandra](../Cassandra.md) > [Query Model](Query Model.md) > Full Text Search Support
___


Cassandra doesn't support internally full-text search. However it supports creating a custom index. Custom indexes can be created by writing a java class that implements Cassandra abstract class org.apache.cassandra.db.index.SecondaryIndex. There are also some external plugins available for full-text search such as the [Stratio’s Cassandra Lucene Index](https://github.com/Stratio/cassandra-lucene-index).