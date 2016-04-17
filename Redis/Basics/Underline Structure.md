



### [Redis ](../Redis.md) > [Basics](Basics.md) > Underline Structure
___



Although Redis is a key-value datastore, it supports more than just the plain mapping of string keys to the string values. Instead it maps a key to different useful data structures that can be used to develop complex use cases. The key itself is binary safe which means it can be either a normal string or even a binary content of a file. However it is a good practice not to use long keys since it will impact performance and increase memory usage. In addition, keys should be consistent and can be used to define namespaces for your applications e.g school:teacher:john. In this section, I am going to give a quick overview of the supported data types that can be used in Redis to store your data.


#### Strings

String is the simplest data type in Redis. It is also the most typically used data type in other key-value data stores. Inside this data type, you can store not only sting but also integers, floating point values or even a binary data such as an image or any file. However, the size of the stored value shouldn't exceed a maximum 512MB of data.

#### Lists

This data type is a sequence of ordered elements that follows the well-known linked list structure. The elements are added only to the head or the tail of the list which is an extremely fast operation and takes a constant time. However accessing the elements by index is not so fast and will requires an amount of work proportional to the accessed index. This data type is the best candidate be used to implement queues.

#### Hashes

Redis hashes can be used to store mappings of keys to values where values are of a String data type. The hash data type in Redis can be used to store complete complex objects and can be compared to the documents in a documents-based database or like a row in a relational database. An example would be to use the hash to store the shopping cart object of an e-commerce application.

#### Sets and Sorted Sets

Sets can be used to store unordered sequence of strings that are unique. It means that if you try to add an already existing element to the set, it will just ignore the operation. Sets support very useful operations such as intersect and union which can be used to group and filter the data. Sorted Sets on the other hand are a special case of the set implementation where it defines a score for each string. The score allows you to get the elements inside the sorted sets by a particular order. Also you can retrieve ordered elements in the sorted set by score range.

#### bitmaps

These aren't actually a separate data type, but a special case of string data type when using a special set of commands. Bitmap commands are mainly used to increase performance by saving memory space when storing information as bits. 

#### HyperLogLogs

Hyperloglogs are probabilistic data structures that are used to count unique items. The idea behind this data type is to avoid storing amount of data in memory that is proportional to the items that need to be counted. Instead by using a special algorithm to store only a constant amount of memory (around 12K bytes in the worse case) but with an error (less than 1% in Redis implementation) of the estimated count. Hyperloglogs are encoded as strings, hence they are sharing similar commands.


