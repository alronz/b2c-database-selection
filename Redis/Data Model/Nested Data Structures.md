[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Data Model > Nested Data Structures


Redis doesn't support nested structures. For instance, the hash fields can only have a string data type and hence can't embed other hashes, sets, sorted sets or lists. However you can still store in the string data type any stream of bytes which can be JSON data but that wont be helpful since you can't query this nested data. 
