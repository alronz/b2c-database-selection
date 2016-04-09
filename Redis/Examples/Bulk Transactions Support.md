[Home](../../index.md)

[Redis Content](../Redis.md)
___

# Redis > Examples > Bulk Transactions Support


In this example, a service was built to show how to execute a batch of commands at the same time. Assuming we want to add all existing logged in customers to a VIP set, then we can do that using a pipeline feature of Redis and execute multiple commands together as shown in the below API implementation:


````
    @GET
	@Path("/addToVIP")
	@Timed
	@ApiOperation(value = "add All current Logged In Customers to VIP ", notes = "Returns execution summery", response = String.class)
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public String addToVIP() {

		try {
			
			long sortedSetSize = this.jedisClient.zcard("login:");
			Set<String> allLoggedInUsers = this.jedisClient.zrange("login:", 0,
					sortedSetSize - 1);

			String[] tokenList = allLoggedInUsers
					.toArray(new String[allLoggedInUsers.size()]);
			Pipeline pipe = this.jedisClient.pipelined();
			ArrayList<Response<Long>> responses = new ArrayList<Response<Long>>();
			for (String token : tokenList) {
				responses.add(pipe.sadd("VIP:", token));
			}
			pipe.sync();

			return checkReponses(responses);

		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"Coudln't access Redis" + e.getLocalizedMessage());
			final String shortReason = "Coudln't access Redis";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
````


As can be seen above, we start a pipeline, execute all the commands and then check the results at once. We check the results, using the below function:

````
private String checkReponses(ArrayList<Response<Long>> responses) {
		int notExecutedCount = 0;
		int executedCount = 0;
		int totalCount = 0;
		for (Response<Long> response : responses) {
			totalCount++;
			if (response.get() == 0) {
				notExecutedCount++;
			} else if (response.get() == 1) {
				executedCount++;
			} else {
				LOGGER.info("response is" + response.get());
			}
		}
		LOGGER.info("out of:" + totalCount
				+ " commands, number of successfully executed commands are:"
				+ executedCount + " and number of errored out commands are:"
				+ notExecutedCount);

		return ("out of:" + totalCount
				+ " commands, number of successfully executed commands are:"
				+ executedCount + " and number of errored out commands are:" + notExecutedCount).toString();
	}
````
