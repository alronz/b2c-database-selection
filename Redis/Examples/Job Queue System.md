

### [Redis ](../Redis.md) > [Examples](Examples.md) > Job Queue System
___

In the example, I will show how to build a queue system functionalities using Redis. Redis supports the list data structure which is very suitable to build queue system. For this a service has been built to support the below APIs:


![image](https://s3.amazonaws.com/b2cbucket/queues.png)


To add a job to queue, the below API can be called:

````
    @POST
	@Path("/enqueue")
	@Timed
	@ApiOperation(value = "enqueue a job ", notes = "Returns the enqueued job", response = Job.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "job wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public Job enqueue(
			@ApiParam(value = "job to be queued", required = true) Job job) {

		if (job == null || job.getQueueName() == null || job.getData() == null) {
			final String shortReason = "job wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			// enqueue the job inside the queue list
			this.jedisClient
					.rpush("queue:" + job.getQueueName(), job.getData());
			// add the queue name to a set
			this.jedisClient.sadd("queues:", job.getQueueName());

			return job;

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

The job is inserted inside the list using the rpush command. A set is used to store the used queue names.


To remove an element from the queue, the queue type needs to be specified. If the queue type is "FIFO", then the job will be removed from the end of the queue. However if the queue type is "LIFO", the job will be removed from the head of the queue as shown below:

````
    @GET
	@Path("/dequeue/{queueName}/type/{queueType}")
	@Timed
	@ApiOperation(value = "dequeue a job ", notes = "Returns the dequeued job", response = Job.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "queue Name or type wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis"),
			@ApiResponse(code = 400, message = "queue type not supported !") })
	public Job dequeue(
			@ApiParam(value = "queue Name to be dequeued", required = true) @PathParam("queueName") String queueName,
			@ApiParam(value = "queue type,FIFO or LIFO", required = true) @PathParam("queueType") String queueType) {

		if (queueName == null || queueType == null) {
			final String shortReason = "queue Name or type wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			Job result = new Job();
			result.setQueueName(queueName);
			if (queueType.equals("FIFO")) {
				// dequeue the job from the queue list
				result.setData(this.jedisClient.lpop("queue:" + queueName));
			} else if (queueType.equals("LIFO")) {
				// dequeue the job from the queue list
				result.setData(this.jedisClient.rpop("queue:" + queueName));
			} else {
				final String shortReason = "queue type not supported !";
				Exception cause = new IllegalArgumentException(shortReason);
				throw new WebApplicationException(cause,
						javax.ws.rs.core.Response.Status.BAD_REQUEST);
			}

			// check if the list is empty then remove it from queues set
			if (this.jedisClient.llen("queue:" + queueName) == 0) {
				this.jedisClient.srem("queues:", queueName);
			}

			return result;

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


It is also possible to peak into the queue using the below API:


````
    @GET
	@Path("/peak/{queueName}/number/{elelementsNumber}")
	@Timed
	@ApiOperation(value = "peak elements from head ", notes = "Returns some elements from head", response = String.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "queue name or elelemnts number wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public List<String> peakHead(
			@ApiParam(value = "queue Name", required = true) @PathParam("queueName") String queueName,
			@ApiParam(value = "elelments number to return", required = true) @PathParam("elelementsNumber") int elelementsNumber) {

		if (queueName == null || elelementsNumber == 0) {
			final String shortReason = "queue name or elelemnts number wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			List<String> results = this.jedisClient.lrange(
					"queue:" + queueName, 0, elelementsNumber);

			return results;

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


To remove a particular job from that can be in the middle of the queue, the lrem command can be used which will search for the job and delete it if it exists as shown below:

````
    @DELETE
	@Path("/DeleteJob")
	@Timed
	@ApiOperation(value = "delete element from a queue ", notes = "Returns the deleted element", response = Job.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "job wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public Job deleteElement(
			@ApiParam(value = "job to be deleted", required = true) Job job) {

		if (job == null || job.getQueueName() == null || job.getData() == null) {
			final String shortReason = "job wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			this.jedisClient.lrem("queue:" + job.getQueueName(), 0,
					job.getData());

			// check if the list is empty then remove it from queues set
			if (this.jedisClient.llen("queue:" + job.getQueueName()) == 0) {
				this.jedisClient.srem("queues:", job.getQueueName());
			}

			return job;

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

Finally, to get the queue names we can use the set that we used previously to store the queue names as shown below:

````
    @GET
	@Path("/queues")
	@Timed
	@ApiOperation(value = "get all queue names", notes = "Returns the name of all queues", response = String.class, responseContainer = "set")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public Set<String> allQueues() {

		try {
			Set<String> results = this.jedisClient.smembers("queues:");

			return results;
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