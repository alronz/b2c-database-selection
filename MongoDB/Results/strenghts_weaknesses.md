In this section, I will talk about the strengths and weaknesses of MongoDB and when using it makes sense.


##### Strengths 

Below are the main strengths of MongoDB:

###### Easy to scale out  

MongoDB supports scaling out your reads and write throughputs using the sharded cluster.  The sharded cluster supports auto sharding and auto balancing which makes it easy to horizontally scale your application by adding thousands of nodes and be able to response to millions of operations per second over a very large data set. 

 
###### Flexible document Data model

The especial document data model of MongoDB allows for more flexible designs since there is no need to convert or map your application objects to database object like we do in relational databases (no impedance mismatch). The objects are easily mapped to BSON documents which is more natural. This makes MongoDB more developer friendly and since MongoDB is schema less, it is easier to change your data structure on the fly by just adding any kind of fields at any time (no ALTER TABLE).


###### Replication and High Availability

MongoDB supports a configuration called a replica set which allows for for data redundancy, fault tolerance and disaster recovery. Additionally, MongoDB supports automatic failover process that takes place when the primary node in a replica set isn't accessible for some reasons. This makes MonoDB easily configurable to be a high available database.


###### Better performance when used correctly

If your data fits the document data model, MongoDB can show a very big improvement in performance. MongoDB encourages denormalising your data into a single document and then it can provide great performance. However it is not always possible to model your data in a denormalised way especially if your data has a relational nature. If your data doesn't fit the document data model,  you can end up with slower performance. But then you have chosen the wrong database. 

###### Rich query capabilities

MongoDB support deep query capabilities such as full-tex search, regular expressions, dynamic queries on documents, aggregation (using map-reduce or the aggregation pipeline framework), and sorting. The aggregation and the queries are performed in the collection-level which might be a problem if you want to run global queries across the whole database since joins aren't supported. 

##### Weaknesses 

Below I will talk about the main weaknesses of MongoDB:

###### Not recommended for relational data

As mentioned before, MongoDB isn't suitable for relational data since joins aren't supported. If the data is so much related and doesn't fit into the document data model, then you will have to normalise the data model as you do in relational databases and this will result in many follow up queries that reduce the performance or introduce code complexity. It is always not recommended to use MongoDB for use cases where you want to model related data and later run complex join-intensive queries. Modelling related data can also lead to so much duplication of the data that can result in documents which increase to unbound limit. When the documents are so large, the performance will degrade and the complexity will increase. Finally, the aggregation and the query in MongoDB is done in the collection level which makes running complex aggregate queries not an easy task.


###### No internal transaction support

MongoDB supports transaction on the document level. However transactions are not automatically supported across different documents. Supporting transactions in MongoDB requires manual intervention to verify, commit or rollback using methods such as the Two Phases Commits pattern. 

##### Summary

MongoDB supports very interesting features such as its unique document data model, scalability, availability and rich query support. This makes it suitable for many use cases such as logging and content management. It is always recommend to use MongoDB for application where the data can fit easily in the document data model. MongoDB isn't a replacement for relational databases, and doesn't support well applications having data with a relational nature such as social data. Besides, MongoDB is not recommended for applications that requires high transaction support such as the critical financial applications. MongoDB excels on applications that requires efficient horizontal scaling, high availability and flexible data model that makes developer's life more easy and at the same time having a data suitable for the document data model.
