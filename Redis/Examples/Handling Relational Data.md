

### [Redis ](../Redis.md) > [Examples](Examples.md) > Handling Relational Data
___


In this example, I will show to model data relationships using Redis assuming the below model:


![image](https://s3.amazonaws.com/b2cbucket/relational_data_redis_model.png)

As you can be seen above, we have four data entities (customer, order, payment and shipping). Each customer can have more than one order and each order can have one payment method and multiple shipping addresses. To model these relationships, we use sets as explained already in the [relational data section](../Data Model/Relational Data Support.md). 

Now in this service, it will answer the below APIs :

![image](https://s3.amazonaws.com/b2cbucket/relational_data.png)


So first the below APIs will be called whenever an order or a customer is added:

* Add customer

````
    @POST
	@Path("/addCustomer")
	@Timed
	@ApiOperation(value = "add a user", notes = "Returns the added customer", response = Customer.class)
	@ApiResponses(value = {
			@ApiResponse(code = 500, message = "Coudln't access Redis "),
			@ApiResponse(code = 400, message = "customerID or details wasn't given! ") })
	public Customer addUser(
			@ApiParam(value = "customer", required = true) Customer customer) {

		if (customer == null || customer.getCustomerID() == null
				|| customer.getOtherCustomerDetails() == null) {
			final String shortReason = "customerID or details wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			this.jedisClient.hset("customers:" + customer.getCustomerID(),
					"customerOtherDetails", customer.getOtherCustomerDetails());

			return customer;

		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"Coudln't access Redis " + e.getLocalizedMessage());
			final String shortReason = "Coudln't access Redis ";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}  
````

* Submit an order

````
    @POST
	@Path("/submitOrder/{customerID}")
	@Timed
	@ApiOperation(value = "submit an order by customer ", notes = "Returns the submitted order ", response = Order.class)
	@ApiResponses(value = {
			@ApiResponse(code = 500, message = "Coudln't access Redis "),
			@ApiResponse(code = 400, message = "order,shipping,payment or customerID weren't given! ") })
	public Order submitOrder(
			@ApiParam(value = "cutomerID", required = true) @PathParam("customerID") String customerID,
			@ApiParam(value = "order to be submitted", required = true) OrderRequest orderInput) {

		Order order = orderInput.getOrder();
		Shipping shipping = orderInput.getShipping();
		Payment payment = orderInput.getPayment();

		if (customerID == null || order == null || order.getOrderID() == null
				|| order.getOtherOrderDetails() == null || payment == null
				|| payment.getPaymentID() == null
				|| payment.getOtherPaymentDetails() == null || shipping == null
				|| shipping.getShippingID() == null
				|| shipping.getOtherShippingDetails() == null) {
			final String shortReason = "order,shipping,payment or customerID weren't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			// set the payment details in a hash
			this.jedisClient.hset("payments:" + payment.getPaymentID(),
					"paymentOtherDetails", payment.getOtherPaymentDetails());
			// set the shipping details in a hash
			this.jedisClient.hset("shippings:" + shipping.getShippingID(),
					"shippingOtherDetails", shipping.getOtherShippingDetails());

			// set the order details in a hash including the one to one
			// relationship with customer and payment
			HashMap<String, String> orderDetails = new HashMap<String, String>();
			orderDetails.put("orderOtherDetails", order.getOtherOrderDetails());
			orderDetails.put("customerID", customerID);
			orderDetails.put("paymentID", payment.getPaymentID());
			this.jedisClient
					.hmset("orders:" + order.getOrderID(), orderDetails);

			// set the many to one relationship between orders and user
			this.jedisClient.sadd(customerID + ":orders", order.getOrderID());

			// set the many to one relationship between orders and payment
			this.jedisClient.sadd(payment.getPaymentID() + ":orders",
					order.getOrderID());

			// set the many to many relationship between orders and shippings
			this.jedisClient.sadd(shipping.getShippingID() + ":orders",
					order.getOrderID());
			this.jedisClient.sadd(order.getOrderID() + ":shippings",
					shipping.getShippingID());

			return order;

		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"Coudln't access Redis " + e.getLocalizedMessage());
			final String shortReason = "Coudln't access Redis ";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
````

Note that we will set the values of the order, payment, and shipping entities whenever an order is submitted. We also need to set the details of the relationships entities with keys (customerID:orders, paymentID:orders, shippingID:orders, and orderID:shippings) which we store them inside a set data structure.

Now we can run the below queries:


* get all customer shippings

````
    @GET
	@Path("/getShippingsPerCustomer/{customerID}")
	@Timed
	@ApiOperation(value = "get all customer's shippings", notes = "Returns list of all customer shippings", response = Shipping.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 500, message = "Coudln't access Redis "),
			@ApiResponse(code = 400, message = "customerID wasn't given! ") })
	public ArrayList<Shipping> getShippingsPerCustomer(
			@ApiParam(value = "cutomerID", required = true) @PathParam("customerID") String customerID) {

		if (customerID == null) {
			final String shortReason = "customerID wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			ArrayList<Shipping> allCustomerShippings = new ArrayList<Shipping>();

			// get all customer orders
			Set<String> setOfCustomerOrders = this.jedisClient
					.smembers(customerID + ":orders");

			String[] orderIDList = setOfCustomerOrders
					.toArray(new String[setOfCustomerOrders.size()]);
			for (String orderID : orderIDList) {
				Set<String> setOfAllShippings = this.jedisClient
						.smembers(orderID + ":shippings");
				String[] shippingIDsList = setOfAllShippings
						.toArray(new String[setOfAllShippings.size()]);
				for (String shippingID : shippingIDsList) {
					Shipping shipping = new Shipping();
					shipping.setShippingID(shippingID);
					shipping.setOtherShippingDetails(this.jedisClient.hget(
							"shippings:" + shippingID, "shippingOtherDetails"));
					allCustomerShippings.add(shipping);
				}
			}

			return allCustomerShippings;

		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"Coudln't access Redis " + e.getLocalizedMessage());
			final String shortReason = "Coudln't access Redis ";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
````

* All the below queries will be answered using this API:

````
// queries

get the orders that are having this paymentID but not this shippingID (difference) ?
get the orders that are having this shippingID or that are having this paymentID (union) ?
get the the orders that are having this paymentID and shippingID at the same time (join) ?

API:


    @GET
	@Path("/getOrdersQuery/{queryType}/payment/{paymentID}/shipping/{shippingID}")
	@Timed
	@ApiOperation(value = "get orders difference, Union, or Join shipping and payment", notes = "Returns list of all orders", response = Order.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 500, message = "Coudln't access Redis "),
			@ApiResponse(code = 400, message = "queryType or shippingID or shippingID wasn't given! "),
			@ApiResponse(code = 400, message = "queryType isn't supported ! ") })
	public ArrayList<Order> getOrdersQuery(
			@ApiParam(value = "how to query, either Difference,Union,Join", required = true) @PathParam("queryType") String queryType,
			@ApiParam(value = "paymentID", required = true) @PathParam("paymentID") String paymentID,
			@ApiParam(value = "shippingID", required = true) @PathParam("shippingID") String shippingID) {

		if (queryType == null || paymentID == null || shippingID == null) {
			final String shortReason = "queryType or shippingID or shippingID wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			ArrayList<Order> resultedOrders = new ArrayList<Order>();
			Set<String> resultedSet;

			if (queryType.equals("Difference")) {

				// get the orders that are having this paymentID but not the
				// shippingID (not in, difference)
				resultedSet = this.jedisClient.sdiff(paymentID + ":orders",
						shippingID + ":orders");

			} else if (queryType.equals("Union")) {

				// get the orders that are having this shippingID or that are
				// having this paymentID (union)
				resultedSet = this.jedisClient.sunion(paymentID + ":orders",
						shippingID + ":orders");

			} else if (queryType.equals("Join")) {

				// get the the orders that are only having this paymentID and
				// shippingID at the same time (join)
				resultedSet = this.jedisClient.sinter(paymentID + ":orders",
						shippingID + ":orders");
			} else {
				final String shortReason = "queryType isn't supported !";
				Exception cause = new IllegalArgumentException(shortReason);
				throw new WebApplicationException(cause,
						javax.ws.rs.core.Response.Status.BAD_REQUEST);

			}

			String[] orderIDList = resultedSet.toArray(new String[resultedSet
					.size()]);
			for (String orderID : orderIDList) {
				Order order = new Order();
				order.setOrderID(orderID);
				order.setOtherOrderDetails(this.jedisClient.hget("orders:"
						+ orderID, "orderOtherDetails"));
				resultedOrders.add(order);
			}

			return resultedOrders;

		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"Coudln't access Redis " + e.getLocalizedMessage());
			final String shortReason = "Coudln't access Redis ";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}  

````




