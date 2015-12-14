#### [back](example_main.md)

As we have already mentioned in the cart management example, using cookies is a good idea for temporary data such as login information and carts. However, the cookies will be sent back and forth between the server and the client with each request. This is usually fine since cookies are most of the time very small but when the cookies are large then this might slow down the requests. Since Redis is a very fast in memory database, we can use it to store login cookies or to handle login sessions. In this example I will be showing how to use Redis to do just that.

To show how to use Redis to manage login sessions, I have created a session management service with the following rest API support:

![image](SessionManagement.png =700x250)


Storing a session in Redis can be achieved by using a simple String data structure where the key is login token and value is the login details. 

* add new session

Adding a new session is done using the below code:

````
    @POST
	@Path("/")
	@Timed
	@ApiOperation(value = "add new session", notes = "Returns the added session", response = Session.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Session details wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't add from Redis !") })
	public Session addSession(
			@ApiParam(value = "Session details", required = true) Session session) {

		if (session == null || session.getToken() == null
				|| session.getData() == null) {
			final String shortReason = "Session details wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			long timestamp = System.currentTimeMillis() / 1000;
			// add the session
			this.jedisClient.set("login:" + session.getToken(),
					session.getData());
			// add it to the recent sorted set with timestamp as score
			this.jedisClient.zadd("recent:", timestamp, session.getToken());
			// expire session each 10 hours
			this.jedisClient.expire("login:" + session.getToken(),
					this.sessionValidityTime);
			// keep only the top 100 recent session
			this.jedisClient.zremrangeByRank("recent:", 0, -101);
			return session;
		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"coudln't add from Redis" + e.getLocalizedMessage());
			final String shortReason = "coudln't add from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
````

So we store the login session inside a Redis string data structure using the set command. We also store the recent login sessions inside a sorted set along with the timestamp so that we can later retrieve the recent sessions sorted by time. Adding an element to a sorted set is achieved by using the zadd command and we use the zremrangeByRank command to keep just the last 100 elements sorted by timestamp and remove the rest which will prevent the set from growing beyond 100 elements. Finally we use expire command to delete the session after particular time (in the example we are using 10 hours).


* Get session details 

To retrieve the session details we use the get Redis command as seen below:

````
    @GET
	@Path("/{token}")
	@Timed
	@ApiOperation(value = "get a session", notes = "Returns a session by token", response = Session.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "token wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't get from Redis !") })
	public Session getSession(
			@ApiParam(value = "token", required = true) @PathParam("token") String token) {

		if (token == null) {
			final String shortReason = "token wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			// get the session by token
			Session result = new Session();
			result.setData(this.jedisClient.get("login:" + token));
			result.setToken(token);

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
````

* Delete the session

We delete the session using the del Redis command. To delete this session from the recent sorted set we can use zrem and providing the session token as seen below:


````
    @DELETE
	@Path("/{token}")
	@Timed
	@ApiOperation(value = "delete a session", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "token wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't delete from Redis !") })
	public Boolean deleteSession(
			@ApiParam(value = "token", required = true) @PathParam("token") String token) {

		if (token == null) {
			final String shortReason = "token wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			// delete the session
			this.jedisClient.del("login:" + token);
			this.jedisClient.zrem("recent:", token);
			return true;
		} catch (Exception e) {
			LOGGER.log(Level.SEVERE,
					"coudln't get from Redis" + e.getLocalizedMessage());
			final String shortReason = "coudln't get from Redis !";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.INTERNAL_SERVER_ERROR);
		}
	}
````

* Get the last 100 recent login sessions

To retrieve the 100 recent login sessions, we can use the recent sorted set that we have been maintaining so far, code is shown below:

````
    @GET
	@Path("/recent100")
	@Timed
	@ApiOperation(value = "get recent 100 sessions", notes = "Returns list of sessions", response = Session.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "coudln't get from Redis !") })
	public ArrayList<Session> getRecent100Sessions() {

		try {
			ArrayList<Session> result = new ArrayList<Session>();

			// get all recent tokens from the recent sorted set
			long sortedSetSize = this.jedisClient.zcard("recent:");
			Set<String> allRecentSessions = this.jedisClient.zrange("recent:",
					0, sortedSetSize - 1);

			String[] sessionTokens = allRecentSessions
					.toArray(new String[allRecentSessions.size()]);
			for (String token : sessionTokens) {
				Session session = new Session();
				session.setToken(token);
				session.setData(this.jedisClient.get("login:" + token));
				result.add(session);
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
````

As can seen above, we used zcard command to get the number of elements inside the set or the set size, then we can retrieve all the elements in the set using zrange command with min as zero and max as the size for the set.



