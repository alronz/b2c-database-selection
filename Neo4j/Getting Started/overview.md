#### [back](getting_started_main.md)





Official definition :


What is Neo4j?

Sponsored by Neo Technology, Neo4j is an open-source NoSQL graph database implemented in Java and Scala. With development starting in 2003, it has been publicly available since 2007. The source code and issue tracking are available on GitHub, with support readily available on Stack Overflow and the Neo4j Google group. Neo4j is used today by hundreds of thousands of companies and organizations in almost all industries. Use cases include matchmaking, network management, software analytics, scientific research, routing, organizational and project management, recommendations, social networks, and more.

Neo4j implements the Property Graph Model efficiently down to the storage level. As opposed to graph processing or in-memory libraries, Neo4j provides full database characteristics including ACID transaction compliance, cluster support, and runtime failover, making it suitable to use graph data in production scenarios.

Some particular features make Neo4j very popular among users, developers, and DBAs:

Materializing of relationships at creation time, resulting in no penalties for complex runtime queries
Constant time traversals for relationships in the graph both in depth and in breadth due to efficient representation of nodes and relationships
All relationships in Neo4j are equally important and fast, making it possible to materialize and use new relationships later on to “shortcut” and speed up the domain data when new needs arise
Compact storage and memory caching for graphs, resulting in efficient scale-up and billions of nodes in one database on moderate hardware
Written on top of the JVM