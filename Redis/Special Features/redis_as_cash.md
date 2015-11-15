If your plan is to use Redis just as a cache system, then instead of expiring keys manually you can just let Redis take care of this automatically. Redis provides a way to easily configure your Redis server to act as a cache.  For instance you can use the below configurations to allow for a maximum of 2mb memory limit and make all keys expire automatically:


````
maxmemory 2mb
maxmemory-policy allkeys-lru
````
The keys will be expired based on the LRU (least recently used) algorithm as long as we hit the 2mb memory limit. This feature will make Redis act similarly like memcached database. For more details about the possible options, please have a look to Redis [documentations](http://redis.io/topics/lru-cache).  
