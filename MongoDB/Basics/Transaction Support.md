

### [MongoDB ](../MongoDB.md) > [Basics](Basics.md) > Transaction Support
___

On the level of a single document, MongoDB guarantees that the write operation is atomic. This means that if you try to update, insert or delete a single document in MongoDB, the operation will be applied in the form of "all or nothing". However if you try to do a write operations for multiple documents, the operations will be applied atomically for each single document alone but the operations as whole may interleave and are not atomic. Hence the transaction is possible using MongoDB if you do it in a single document and not supported if you plan to do it across multiple documents.  

Although MongoDB supports a way to isolate a write operation if it will be applied across multiple documents using the $isolated option as seen in the below example, this is not always enough to support transaction across multiple documents. 

````
db.products.update(
    { color : "Red" , $isolated : 1 },
    { $inc : { price : 1 } },
    { multi: true }
)
````
In the above example, we are increasing the price of all products having the red colour. This method might update multiple documents since we are using the "multi" option, however the operation will take a write lock for all affected documents to prevent any concurrent read or write access to them during the operation. Although this will provide an isolation level for the affected documents, but it doesn't execute the operation atomically "all or nothing" and hence doesn't totally support a transaction.


To summarised, MongoDB supports transaction well on the level of a single document. However for transactions across multiple documents, transaction  is not internally supported by MongoDB but there is a way to get around this limitation as I will explain in the following sections. 

#### Transaction using a single document

Since MongoDB supports embedding documents inside other documents, you can model your data in a single document to support atomic operations. To give an example, let's assume you want to check out some product in an e-commerce application. We would first check if there is still amount of this product in the inventory before checking out. If we store all this information in a single document as seen below, then we can ensure an atomic transaction.

 
 ````
 {
    sku: SomeSkuID,
    name: "TShirt",
    size: 20,
    quantity: 3,
    checkout: [ { by: "SomeUserID", date: ISODate("2015-12-15") } ]
}
 ````
 
 As seen above, we are storing the quantity of this product in the same document and the information about the users who have checked out this product previously.
A transaction will be atomically executed using the below method:
 
  ````
 db.products.update (
   { sku: SomeSkuID, quantity: { $gt: 0 } },
   {
     $inc: { quantity: -1 },
     $push: { checkout: { by: "OtherUserID", date: new Date() } }
   }
)
 ````

Then you can simply check the WriteResult() returned from this operation to check if it has been successful or not.


#### Transaction across multiple documents

We can do a transaction across multiple documents if we use the Two Phases Commits pattern which is very well explained in [MongoDB documentations.](https://docs.mongodb.org/manual/tutorial/perform-two-phase-commits/)
The idea behind this pattern is to use state transition for each stage you want to execute. This can be also easily extended to provide rollback-like functionality. A simple example is explained below:

To show an example for how to support transaction across multiple documents in MongoDB, we will show how you can add a product to the cart and modify the inventory accordingly. Assuming we have the below schemas for cart and inventory documents:


Cart document schema:

 ````
{
    cartID: SomeID,
    items: [
        { sku: '1', qty: 1, item_details: {...} },
        { sku: '2', qty: 2, item_details: {...} }
    ]
} 
````
 
 Inventory document schema:
 
 
  ````
 {
    sku: 'ProductSKU',
    qty: 10,
 }
 ````
 
Then to add a product into the cart, you will add it to the cart and then you decrease the quantity from the inventory if the quantity is available and if not you rollback as seen below:



 
````
boolean addToCart(String cartID,String sku,int quantity, HashMap<String, String> details)
{
   Result result;

   // Add to cart

   try{

   result = db.cart.update(
        {'cartID': cartID },
        {'$push':{ 'items': {'sku': sku, 'qty':quantity, 'details': details}}});

     }Catch(Exception e)
     {
     
     raiseNoCartExist();
     return false;
     
     }        
      
    // Update the inventory

    try{

    result = db.inventory.update(
        {'sku':sku, 'qty': {'$gte': quantity}},
        {'$inc': {'qty': -quantity});
        
    }Catch(Exception e)
    {
    
    db.cart.update(
       {'cartID': cartID },
       { '$pull': { 'items': {'sku': sku } } })
    raiseNoEnoughInventory(); 
    return false;
    
    }
              
    return true;
  }        
 ````


#### Concurrency 

Concurrency control in MongoDB means how to allow multiple applications to access documents concurrently without causing inconsistency or conflicts. This is usually done by creating a unique index on one of the document field so that any concurrent insert or update for this document won't result in a duplicate document. 


