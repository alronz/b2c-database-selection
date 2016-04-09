[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Examples > Transaction Support


In this example, we show how we can execute command in a transaction using the MULTI/EXEC/WATCH/UNWATCH commands. In this example, we try to execute a transaction where we take a product from the inventory of the seller and put in the bought list of the customer. The code is shown below:

````

    @GET
	@Path("/buy/{sku}/seller/{sellerID}/token/{token}")
	@Timed
	@ApiOperation(value = "buy a product from seller ", notes = "Returns execution summery", response = String.class)
	@ApiResponses(value = {
			@ApiResponse(code = 500, message = "Coudln't access Redis "),
			@ApiResponse(code = 400, message = "sku or sellerID wasn't given! "),
			@ApiResponse(code = 400, message = "seller inventory is empty ") })
	public String buyProduct(
			@ApiParam(value = "product sku", required = true) @PathParam("sku") String sku,
			@ApiParam(value = "seller id", required = true) @PathParam("sellerID") String sellerID,
			@ApiParam(value = "token", required = true) @PathParam("token") String token) {

		if (sku == null || sellerID == null) {
			final String shortReason = "sku or sellerID wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			ArrayList<Response<Long>> responses = new ArrayList<Response<Long>>();

			this.jedisClient.watch("inventory:" + sellerID);
			if (!this.jedisClient.sismember("inventory:" + sellerID, sku)) {
				this.jedisClient.unwatch();

				final String shortReason = "seller inventory is empty";
				Exception cause = new IllegalArgumentException(shortReason);
				throw new WebApplicationException(cause,
						javax.ws.rs.core.Response.Status.BAD_REQUEST);

			}

			Transaction transaction = this.jedisClient.multi();

			responses.add(transaction.srem("inventory:" + sellerID, sku));
			responses.add(transaction.sadd("bought:" + token, sku));

			transaction.exec();

			return checkReponses(responses);

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

As seen above, we first watch the inventory of the seller to ensure the consistency of the data by making sure that no one is changing the inventory at the same time. Then we start a transaction using the multi command and adding command to the transaction (removing a product from the seller inventory and adding it to the bought list of the customer). Finally, we execute the transaction using the exec command. Later we can check the result of executing this transaction using the checkReponses() function.


