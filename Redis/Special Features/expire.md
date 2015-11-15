In Redis you can set a timeout on a key. It means that the key will be automatically deleted after the timeout expires. This feature makes Redis a good database to implement cashing and similar applications. Using this feature is so easy and can be done using the command EXPIRE and by providing the timeout as shown in the example below:

````
redis> SET mykey "Hello"
OK
redis> EXPIRE mykey 10
(integer) 1
````

To know how much time is left for the key to be deleted, you can use the command TTL which will give you the remaining time to live for a certain key. The timeout can be also removed later if needed to persist the key again using the command PERSIST as shown below:


````
redis> SET mykey "Hello"
OK
redis> EXPIRE mykey 10
(integer) 1
redis> TTL mykey
(integer) 10
redis> PERSIST mykey
(integer) 1
redis> TTL mykey
(integer) -1
````

If you want to expire the key at a certain time instead of providing a timeout, you can use EXPIREAT which expires a certain key at a specific timestamp as seen below:

````
redis> SET mykey "Hello"
OK
redis> EXISTS mykey
(integer) 1
redis> EXPIREAT mykey 1293840000
(integer) 1
````
