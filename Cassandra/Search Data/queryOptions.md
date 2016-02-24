#### [back](search_data_main.md)


Cassandra has a built-in support for common aggregate functions such as min, max, avg, sum, and count.  Cassandra also supports the creation of custom user defined functions. In this section, I will explain how to use the built-in functions as well as the user defined functions.


Assuming we have created the below order table:

````
CREATE TABLE order (id UUID, items set<text>, total decimal, order_year, status text PRIMARY KEY (order_year,total);
````

Then you can get the sum of all orders totals for the year 2015 as shown below:

````
SELECT sum(total) FROM order WHERE order_year='2015';
````

Or get the average price of all the orders in the year 2015:

````
SELECT avg(total) FROM order WHERE order_year='2015';
````

Or get the min or max order total price for the year 2015:

````
SELECT min(total) FROM order WHERE order_year='2015';
````

````
SELECT max(total) FROM order WHERE order_year='2015';
````

And to get the count of the orders in 2015:

````
SELECT count(*) FROM order WHERE order_year='2015';
````

Finally you can limit your returned results using the LIMIT clause as shown below:

````
SELECT * FROM order WHERE order_year='2015' LIMIT 100;
````

In the above query, we are returning the first 100 results.





Cassandra also supports user defined functions. Assuming we want to get also the TAX for each order. Then we can create a custom user defined function to support this as seen below:


````
CREATE OR REPLACE FUNCTION fTax (input double,taxPercentage int) CALLED ON NULL INPUT RETURNS double LANGUAGE java AS 'return Double.valueOf(input.doubleValue() * taxPercentage);';
````

Then we can run a query as below:


````
SELECT id,fTax(total, 0.06) FROM order WHERE order_year='2015' ;
````






