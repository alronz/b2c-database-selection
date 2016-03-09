#### [back](example_main.md)

In this example, I will show how to use Redis to store the shopping cart of a B2C application. Usually the shopping cart data is stored in the client side as a cookie. The advantage of doing this is that you wouldn't need to store such a temporary data in your database. However this will require you to send the cookies with every web request which can slow down the request in case of large cookies. Storing shopping cart data in Redis is a good idea since you can retrieve them very faster at any time and persist this data is possible if needed.


To show how you can use Redis to store shopping cart information, I have created a shopping cart management service which will support the below rest APIs:

![image](https://s3.amazonaws.com/b2cbucket/CartManagementAPi.png)


The cart can be stored easily in Redis inside a Hash data structure. In the example I used, the cart has many cart items and each cart item has the below fields:

```
private String sku;  // the sku of the product
private Double amount; // the amount from this product
private Double price; // the price of the product
```

So we store for each user session, a cart object inside a hash with a key like "cart:{sessionID}". Inside this hash, we store the cart-item's sku as a key and the value is the item details in JSON format.

![image](https://s3.amazonaws.com/b2cbucket/cart.png)

* Add cart item to cart:


The customer can add items to his cart by calling /cart/add/{session} and the below will be executed:

```
    @POST
	@Path("/add/{session}")
	@Timed
	@ApiOperation(value = "add new cart item", notes = "Returns the added Cart Item", response = CartItem.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Cart item details or session wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't add or delete from Redis !") })
	public CartItem addCartItem(
			@ApiParam(value = "Session id", required = true) @PathParam("session") String session,
			@ApiParam(value = "Cart Item", required = true) CartItem cartItem) {

		if (session == null || cartItem.getSku() == null
				|| cartItem.getPrice() == null || cartItem.getAmount() == null) {
			final String shortReason = "Cart item details or session wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			if (cartItem.getAmount() == 0) {
				jedisClient.hdel("cart:" + session, cartItem.getSku()
						.toString());
			} else {
				jedisClient.hset("cart:" + session, cartItem.getSku()
						.toString(), cartItem.toJson());
				jedisClient.expire("cart:" + session, this.cartValidityTime);
			}

			return cartItem;
		} catch (Exception e) {
			LOGGER.log(
					Level.SEVERE,
					"coudln't add or delete from Redis"
							+ e.getLocalizedMessage());
			final String shortReason = "coudln't add or delete from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
```

As you can see it is quite simple, we just need to insert the cart item inside the user's cart using hset command. Before that we need to check if the amount of this item is zero, then we will update the cart by removing this item. Otherwise, we will add the item to the cart using hset command.  We also expire this cart after some cart validation time using the expire command so that we don't persist the cart information forever. In this example we expire the cart each 10 hours.


* delete item from the cart

Deleting a product from the user's cart is similar to adding the product into the cart but we are using hdel command instead as seen below:

```
    @DELETE
	@Path("/delete/{session}")
	@Timed
	@ApiOperation(value = "delete cart item from cart", notes = "Returns the deleted Cart Item", response = CartItem.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Cart item details or session wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't add or delete from Redis !") })
	public CartItem deleteCartItem(
			@ApiParam(value = "Session id", required = true) @PathParam("session") String session,
			@ApiParam(value = "Cart Item", required = true) CartItem cartItem) {

		if (session == null || cartItem.getSku() == null) {
			final String shortReason = "Cart item details or session wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			jedisClient.hdel("cart:" + session, cartItem.getSku().toString());

			return cartItem;
		} catch (Exception e) {
			LOGGER.log(
					Level.SEVERE,
					"coudln't add or delete from Redis"
							+ e.getLocalizedMessage());
			final String shortReason = "coudln't add or delete from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
```


* Get cart details

To get the cart details, we use the Redis command hgetAll which will return all the products inside the cart as seen below:

```
    @GET
	@Path("/{session}")
	@Timed
	@ApiOperation(value = "get cart", notes = "Returns the cart details", response = CartItem.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "session wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't get from Redis !") })
	public ArrayList<CartItem> getCart(
			@ApiParam(value = "Session id", required = true) @PathParam("session") String session) {

		if (session == null) {
			final String shortReason = "session wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}
		ArrayList<CartItem> result = new ArrayList<CartItem>();

		try {

			Map<String, String> restul = jedisClient.hgetAll("cart:" + session);
			Iterator it = restul.entrySet().iterator();
			while (it.hasNext()) {
				CartItem cart = new CartItem();
				Map.Entry pair = (Map.Entry) it.next();
				JsonObject jObject = new JsonParser().parse(
						pair.getValue().toString()).getAsJsonObject();
				cart.setSku(jObject.get("sku").toString());
				cart.setAmount(jObject.get("amount").getAsDouble());
				cart.setPrice(jObject.get("price").getAsDouble());
				result.add(cart);
				it.remove(); // avoids a ConcurrentModificationException
			}

			return result;
		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"coudln't get from Redis" + e.getLocalizedMessage());
			final String shortReason = "coudln't get from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}

```

* Delete the whole cart

To delete the cart, we need to delete the whole cart hash by using the expire command where time is zero as shown below:

```
    @DELETE
	@Path("/{session}")
	@Timed
	@ApiOperation(value = "delete cart", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "session wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't delete from Redis !") })
	public boolean deleteCart(
			@ApiParam(value = "Session id", required = true) @PathParam("session") String session) {

		if (session == null) {
			final String shortReason = "session wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			this.jedisClient.expire("cart:" + session, 0);

			return true;
		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"coudln't delete from Redis" + e.getLocalizedMessage());
			final String shortReason = "coudln't delete from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
```
 






