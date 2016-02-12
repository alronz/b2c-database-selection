#### [back](search_data_main.md)


Cassandra doesn't support internally full-text search. However it provides the ability to create a custom index where you can write a java class for your custom index that implements Cassandra abstract class org.apache.cassandra.db.index.SecondaryIndex. There are also some plugins available for full-text search such as the [Stratio’s Cassandra Lucene Index](https://github.com/Stratio/cassandra-lucene-index).