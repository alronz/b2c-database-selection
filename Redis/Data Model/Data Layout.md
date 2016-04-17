



### [Redis ](../Redis.md) > [Data Model](Data Model.md) > Data Layout
___


In case of relational databases, we need to think about our data layout and do a schema design before start interacting with our database. However in Redis there is no schema and hence no need for data design. We just need to think about our underline data structure that we need to store. Then we need to think about which data types we need to use in order to represent this data. An example would be to model an article voting system. We would store the article object in Hash. Then we can store the list of articles ordered by votes in a sorted sets where score is the number of votes. Finally a normal set can store the name of the users who have voted for a particular article.