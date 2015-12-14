#### [back](basic_features_main.md)

As explained in the [Query Language section](commands.md), MongoDB provides methods to update,remove,replace or insert multiple documents at the same time such as db.collection.insertMany(), db.collection.updateMany(), and db.collection.deleteMany(). In addition, MongoDB supports another method that can be used to execute a bulk of write operations such as insert, update and delete at the same time. In the following section, I will explain how to do a bulk write in MongoDB.

####  db.collection.bulkWrite()

This method provides you with a bulk write capability in a certain collection. You can use this command if you want to run multiple insert,update,replace and delete commands at the same time. These commands can be executed in order or out of order. Executing commands in order means that they will be executed sequentially and when an error occur, it will stop and no further command will be executed while executing commands out of order will just through an error and continue executing other commands. However executing commands in order is slower than out of order specially if you have multiple shards. An example is given below to show how to use this command:

````
try {
   db.products.bulkWrite(
      [
         { insertOne :
            {
               "document" :
               {
                  "_id" : 1, "name"" : "Cool TShirt", size": ""22"", "price" : 20
               }
            }
         },
         { insertOne :
            {
               "document" :
               {
                  "_id" : 2, "name" : "laptop", "size" : "125", "price" : 230
               }
            }
         },
         { updateOne :
            {
               "filter" : { "size" : "$lt:20" },
               "update" : { $set : { "type" : "small" } }
            }
         },
         { deleteOne :
            { "filter" : { "name" : "Cool TShirt"} }
         },
         { replaceOne :
            {
               "filter" : { "name" : "Laptop" },
               "replacement" : { "name" : "Dell Laptop", "size" : "222", "price" : 500 }
            }
         }
      ]
   );
}
catch (e) {
   print(e);
}
````

By default MongoDB consider the bulk write as ordered unless specified otherwise. So the above example will be executed sequentially and will return a result like below:

````
{
   "acknowledged" : true,
   "deletedCount" : 1,
   "insertedCount" : 2,
   "matchedCount" : 2,
   "upsertedCount" : 0,
   "insertedIds" : {
      "0" : 1,
      "1" : 2
   },
   "upsertedIds" : {

   }
}
````


You can also use a write concern which describe the level of acknowledgement request from MongoDB for these write operations. For more details on using the bulk write method, please have a look to [MongoDB documentations](https://docs.mongodb.org/manual/core/bulk-write-operations/). 
