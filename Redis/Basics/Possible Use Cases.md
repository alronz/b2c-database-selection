


### [Redis ](../Redis.md) > [Basics](Basics.md) > Possible Use Cases
___


Generally many users prefer to choose redis or any other in-memory database to store their data only when performance or the supported data structures used in Redis is necessary. Otherwise they tend to use other databases for data where slower performance is not a problem or when the data is so big to fit in memory economically. 
Redis is a high performance database that can be scaled easily to hundreds of gigabytes of data and millions of requests per second. It also provides on-disk persistence and supports very unique data structures which makes it suitable for a variety of applications such as caching, as a message broker, as a chat server, in session management, as a distributed lock system, to implement queues, as a logging system, in any counting related applications,  in real time analytics, to implement leaderboards, as a voting system and many other applications.

Redis can be used efficiently on ecommerce applications for cashing, session management, shopping cart management, real time analytics on cart, sessions, or in product related analytics such as to identify hot products or most and recent viewed products. I will explain in the examples section the complete implementation for a caching, session, shopping cart, and analytics management services that can be used on e-commerce  applications.