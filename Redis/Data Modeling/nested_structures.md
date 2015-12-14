#### [back](data_modeling_main.md)

Redis doesn't support the ability to nest structures. For instance hashe's fields can be only of string data type and can't contain other hashes,sets,sorted sets or lists. Although this is not supported, you can still put in the string data type any stream of bytes which can be JSON data or some other serialization library of your choice but that wont be helpful since you can't query this data using Redis. 
