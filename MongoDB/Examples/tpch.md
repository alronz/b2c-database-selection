In this example, I will show how we can model a complete B2C data and perform more complex queries on it. I am going to use in this example the model used by the [TPC-H benchmark](http://www.tpc.org/tpch) which is shown below.


![image](tpch_schema.png)





To model this schema using MongoDB, we can either use the denormalised model,the normalised model or a combination of both. To show the impact of data modelling in MongoDB, I have modelled the schema using all three models:

1- [a fully denormalised model](denormalised_model.md).
2- a fully normalised model.
3- a mix of normalised and denormalised models. 