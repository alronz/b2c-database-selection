### Underline data structures:

Although Redis is a key-value datastore, it supports more than just the plain mapping of string keys to the string values. Instead it maps a key to differnt useful data structures that can be used to develop complex use cases. The key itself is binary safe which means it can be either a normal string or even a a binary content of a file. However it is a good practice not to use long keys since it will impact performance and increase memory usage. In addition, keys should be consistent and can be used to define namespaces for your applications e.g school:teacher:john. In this section, I am going to give a quick overview of the supported data types that can be used as values in Redis to store your data.


* #### Strings

String is the the simplest data type in Redis is a string. It is also the typical used in other key-value data stores. You can store not only sting but also integers , floating point values or even a binary data such as an image or any file. However, the size of the stored value shouldn't exceed 512MB of data.

* #### Lists

This data type is a sequence of ordered elements that follows that structure a linked list. The elements are added only to the head or the tail of the list which is an exteremly fast operation and takes constant time. However accessing the elements by index is not so fast and will requires an amount of work proportional to the accessed index. This data type is the best candidate be used to implement queues.

* #### Hashes

Redis hashes can be used to store a mapping of keys to values where values are the same can be stored on a String data type. The hash data type in Redis can be used to store complete complex objects and can be compared to the documents in a documents-based database or like a row in a relational database. An example would be to store the shopping cart object of an e-commerce application in a hash as we are going to show how to do that in later sections.

* #### Sets and Sorted List

List can be used to store unordered sequence of strings that are unique. It means that if you try to add an element that is already in the set , it will just ignore the operation. Sets support very useful operations such as intersect and union. Sorted list on the other hand are a special case of the set implementation where it define a score for each string. The score allows you to get the elements inside the sorted sets by a particular order. Also you can retrieve ordered elements in the sorted list by score range.

* #### bitmaps

These aren't actually a seperate data type, but a special case of string data type when using a special set of commands. Bitmap commands are mainly used to increase performance by memory space saving when storing information. 

* #### HyperLogLogs

Hyperloglogs are probabilistic data structures that are used to count unique items. The idea behind this data type is to avoid storing amount of data in meomery that is proportional to the items that need to be counted. Instead by using a special algorithm to store only a constant amount of memory (around 12K bytes in the worse case) but with an error (less than 1% in Redis implementation) of the estimated count. Hyperloglogs are encoded as strings ,hence they are sharing similar commands.


