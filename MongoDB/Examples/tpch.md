In this example, I will show how we can model the data of a complete B2C application and how to write complex queries on this data model. I am going to use the data model used by the [TPC-H benchmark](http://www.tpc.org/tpch) which is shown below.


![image](tpch_schema.png)


The idea behind this example is to show how to use MongoDB to design a real B2C application data model and how to write advance SQL queries with joins, grouping and sorting using MongoDB query language. I have chosen three queries from the TPCH benchmark that I think will cover most of the common query capabilities of SQL. The chosen queries are Q1, Q3 and Q4 as will be explained in the following sections.


The data used as input for this example is a small set from the data generated using TPCH DBGEN tool. The data is stored in CSV files that correspond to each object in the TPCH benchmark schema shown above (customer, supplier, part, lineitem, order , etc ...). For more details about how to generate the data, please have a look at [this](http://kejser.org/tpc-h-data-and-query-generation/) blog post. 

The complete code of this example can be found in Github along with the steps on how to run it. You can also change the used input data by changing the content of the [input files](https://github.com/alronz/B2C-Database-Selection-Implementations/tree/master/MongoDB/TPCHQueries/src/main/java/org/mongoDB/tpcHQueries/data) stored in the "data" folder.



To model this schema using MongoDB, we can either use a denormalised model,a normalised model or a combination of both. To show the impact of data modelling in queries expressiveness, I have modelled the schema using all three models:

 - [a fully denormalised model](denormalised_model.md).
 - [a fully normalised model](normalized_model.md).
 - [a mix of normalised and denormalised models](mixed_model.md). 
 
 
 
 We can see from the above three models that we can use multiple ways to model our data in MongoDB. However, the way you model your data will impact your queries. For example, using the fully normalised way, it was difficult to run aggregate queries and we had to create a temporary collection for each query since mongoDB supports only aggregation on the collection level. Using the fully denormalised way provided us with more flexibility to run aggregate queries since we run them against the same collection. However, using the denormalised model might lead to issues when the document size increases to a very large size. In the above data model, it doesn't seem that the document can increase to an unbound size even if the order contain many lineitems. MongoDB supports up to 16MB for a single document which is I think enough for the above use case. Using the mixed approach was ok for some aggregation queries (Q1 and Q4) but we had to go and create a temporary collection to run Q3 due to the relationship between the order and the customer which was referenced and not embedded in the order document.