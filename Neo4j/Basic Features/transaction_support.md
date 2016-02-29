#### [back](basic_features_main.md)

Neo4j is compliant with the ACID properties and provides full transaction support. All operations in Neo4j that changes the graph will be run on a transaction. This means that an update query will be either fully executed or not at all. If you want to execute multiple update statement in one transaction, the following steps needs to be executed:

1- First open a transaction.

2- Run multiple update statements.

3- Commit all of them if no problem occurs.

4- Rollback if a problem occurs.


Usually to execute transaction, we use a try-catch block where we commit at the end of the try block or rollback inside the catch block. Transactions in Neo4j are having read-committed isolation level which means that we can read only the committed changes.

Default write locks are used in Neo4j whenever we create,update or delete a nodes or a relationships. The locks are added to the transaction and released whenever the transaction completes. However explicit write locks for nodes and relationships can also be enabled to provide higher serialisation isolation level that could be useful for some use cases.


For example, to start a transaction using Cypher HTTP endpoint we issue a POST HTTP request as below:

````
POST http://localhost:7474/db/data/transaction
Accept: application/json; charset=UTF-8
Content-Type: application/json
````

And the body will have the transaction statements:

````
{
  "statements" : [ {
    "statement" : "CREATE (n {props}) RETURN n",
    "parameters" : {
      "props" : {
        "id" : "OrderID",
        "status": "Submitted",
        lineitems: ["productID"]
      }
    }
  }, 
  {
    "statement" : "MATCH (n { props }) SET n.quantity = n.quantity - 1 RETURN n",
    "parameters" : {
      "props" : {
        lineitemID: 'productID'
      }
    }
  }]
}
````

You will get a response as shown below:

````
201: Created
Content-Type: application/json
Location: http://localhost:7474/db/data/transaction/1
````

Then to commit the transaction, you can issue an HTTP request as shown below:


````
POST http://localhost:7474/db/data/transaction/1
Accept: application/json; charset=UTF-8
Content-Type: application/json
````

And the response will be similar to shown below:

````
200: OK
Content-Type: application/json
````


To rollback the transaction you can issue a DELETE request as shown below:

````
DELETE http://localhost:7474/db/data/transaction/1
Accept: application/json; charset=UTF-8
````

Response:

````
200: OK
Content-Type: application/json; charset=UTF-8
````
