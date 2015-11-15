In case of relational databases, we need to think about our data layout and do a schema design before start interacting with our database. However in Redis there is no schema and hence no need to design. We just need to think about our objects that we need to store and think of what key data type is needed to represent them. An example would be if we model an article voting system by storing the article object in Hash. So article:1 will have many fields such as title , description and votes inside the hash. Then we can store the list of articles ordered by votes in a sorted sets where score is the number of votes. Finally a normal set can store the name of the users who have voted for a particular article. 