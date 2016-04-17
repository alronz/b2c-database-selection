

### [Cassandra](../Cassandra.md) > [Data Model](Data Model.md) > Relational Data Support

___


Cassandra doesn't support joins which are used in relational databases to model relationships. Therefore, as explained in the previous section, it is absolutely ok to denormalise your data by creating more than one table for your queries and duplicate the data in each one of them. In this section, I will explain how to model relationships in Cassandra by walking through one simple example from a B2C application domain. 


Assuming you are having a B2C application where you want to run queries that needs to be run across different entities and through a many to many relationship. For example, we have two entities, a customer and a product and there is a many to many relationship between them. The customer can buy one or more products and the product can be bought by one or more customers. Assuming we want to run the below join queries :

Q1) get all the products that were bought by a certain customer

Now depending on how frequent this query will be running and which data to be returned from the query, we can a table to serve this query. Assuming we just want to get the product names and this query will be executed so frequently, maybe thousands of times. Then we will create a separate table having duplicate information from the customer and the product tables as shown below:

````
create table bought_products_by_customer (
      customer_id text,
      product_id text,
      product_name text,
      PRIMARY KEY(customer_id,product_id)      
  );
````

Then you can easily run queries as below:


````
SELECT product_name from bought_products_by_customer where customer_id =1
````

If you want to get the product name, size and color , then you can duplicate this data as well in the bought_products_by_customer table as shown below:

````
create table bought_products_by_customer (
      customer_id text,
      product_id text,
      size text,
      color text,
      product_name text,
      PRIMARY KEY(customer_id,product_id )      
  );
````

Then you can easily run queries as below:


````
SELECT size,color,product_name from bought_products_by_customer where customer_id =1
````


Q2) get all customers that bought a certain product

This is the other way around for the first query and can be done in a very similar way as shown below:


````
create table customers_bought_product (
      product_id text,
      customer_id text,
      name text,
      address text,
      age text,
      PRIMARY KEY(product_id,customer_id)      
  );
````

Then you can easily run queries as below:


````
SELECT name,address,age from customers_bought_product where product_id =1
````



