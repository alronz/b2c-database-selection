#### [back](search_data_main.md)


In many use cases, we will need a way to aggregate values across multiple documents, compute some operations on the aggregated values and then return  results.  MongoDB supports three ways to do just that, either through the pipeline aggregation framework, or by using the map reduce data processing paradigm or through some supported uses-specific functions such as distinct, group and count functions that have been already explained in [previous section.](queryOptions.md)


In the following sections, I will show briefly how to group and filter your data using the pipeline framework and the map reduce data processing model.

##### Aggregation pipeline framework.

The idea behind the aggregation pipeline framework is that the data is processed through multiple stages before a final results is returned. The pipeline works on the collection level as most of MongoDB methods. MongoDB supports many stages to group, match, filter and sort the data such as $project, $match, $group, $sort and $sample stages. For the complete list of all the supported stages, please have a look at [MongoDB documentation.](https://docs.mongodb.org/manual/reference/operator/aggregation/#aggregation-pipeline-operator-reference)

MongoDB supports the db.collection.aggregate() method that operates on a single collection and can take advantage of the already created indexes to enhance performance.


To show how the aggregation framework could be useful, I will explain how to use it to get the price sum of all orders grouped by the order status and consider only orders with price more than 10. The aggregation query is shown below:

````
db.orders.aggregate( [
   { $match: { orderPrice: { $gte: 10 } } } ,
   { $group: { _id: "$orderStatus", priceSum: { $sum: "$orderPrice" } } }
] )
````

In the above example, the first stage is the $match stage which acts as a query filter to return only the documents that match the condition. Then the second stage is the $group stage which will group orders by their order status and the sum of all order price per group. The returned result will be as below:


````
{
_id: "Completed",
priceSum: "129000"
},
{
_id: "Open",
priceSum: "20000"
},
{
_id: "Shipped",
priceSum: "25360"
},
{
_id: "Cancalled",
priceSum: "2300"
}
````


##### Map-Reduce

In general, using the aggregation pipeline framework is the more preferred way to do aggregation related tasks since it is less complex and more efficient than using map-reduce. However map-reduce provides some sort for flexibility since it is using custom javascript functions to do the map and reduce operations.


The map-reduce is basically a two phases operations, the map operation is used to process each document and emit some key-value records that are consumed by the reduce operation to compute and return some results. There is an optional finalise stage to do some final modification the returned results if needed.

MongoDB provides the mapReduce database command that can be executed on the collection level. I will show below how to implement the same example we used previously in the aggregation pipeline framework section using map-reduce:

````
db.orders.mapReduce(
                     function(){ emit( this.orderStatus,this.orderPrice)},
                     function(key,values){ return Array.sum(values)},
                     { query: {orderPrice: {$gte: 10} },
                       out: "orders_status_totals" 
                     }
                   )
````

In the above example, we will first execute the query part to get all products with price greater than or equal to 10. Then these matched documents will be processed by the map operation to get the order status and all order prices with this status. Finally the reduce function will  sum all the order prices for each order status and return result like below:

````
{
_id: "Completed",
value: "129000"
},
{
_id: "Open",
value: "20000"
},
{
_id: "Shipped",
value: "25360"
},
{
_id: "Cancalled",
value: "2300"
}
````



