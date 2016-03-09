Redis is suitable to get real-time analytics since it can answer queries very fast. In a B2C application, it is possible to use Redis to answer queries such as what are the most viewed products, how many users currently logged in, what are the last viewed products, and what are the most  added to cart or deleted from cart products. Of course, we can answer such queries using any other database. However, Redis is much faster since it is in-memory database and can delete the data used for the queries automatically after sometime when it is not needed. For example, if you want to store the most viewed 100 products, you can use the sorted set to store only the 100 most viewed products as will be shown in this section. So Redis is more efficient in terms of performance and storage when it comes to analytics assuming you want to answer simple direct questions.


To show how to use Redis to answer analytical queries, a service has been build that will cover the below APIs:

![image](https://s3.amazonaws.com/b2cbucket/analytics.png)




As seen above, the service will answer the following questions:

````
// queries related to the products
what are the most 10 viewed products for the current user ?
what are the most 20 viewed products in general ?
what are the most 10 recently viewed products by a user ?
what are the most 20 recently viewed products in general ?

// queries related to the cache performance
what are the most 100 requested pages that was found in the cache ?
what are the most 100 requested pages that was not found in the cache ?

// queries related to the cart
what are the most 20 products added to cart ?
what are the most 20 products removed from cart ?

// queries related to the log in
how many users are currently logged in ?
````

For the first set of queries related to the products, the below API will be called whenever a product is being viewed by a user.


````
    @POST
	@Path("/item_viewed/{sku}/token/{token}")
	@Timed
	@ApiOperation(value = "record that item is veiwed", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Sku or token aren't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public boolean recordItemViewed(
			@ApiParam(value = "sku of viewed item", required = true) @PathParam("sku") String sku,
			@ApiParam(value = "login token of the user", required = true) @PathParam("token") String token) {

		if (sku == null || token == null) {
			final String shortReason = "sku or token aren't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			// set the recent products viewed by a user
			long timestamp = System.currentTimeMillis() / 1000;
			this.jedisClient.zadd("viewed:" + token, timestamp, sku);
			// just keep the last 10 recently viewed item for the user
			this.jedisClient.zremrangeByRank("viewed:" + token, 0, -11);
			// keep the most viewed items by user
			this.jedisClient.zincrby("MostViewed:" + token, -1, sku);
			// just keep the last 10 most viewed items by the user
			this.jedisClient.zremrangeByRank("MostViewed:" + token, 0, -11);
			// keep the recently viewed items by all users
			this.jedisClient.zadd("viewed:", timestamp, sku);
			// just keep the last 20 recently viewed items by all users
			this.jedisClient.zremrangeByRank("viewed:", 0, -21);
			// keep the most viewed items by all users
			this.jedisClient.zincrby("MostViewed:", -1, sku);
			// just keep the last 20 most viewed items by all users
			this.jedisClient.zremrangeByRank("MostViewed:" + token, 0, -21);

			return true;
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

As can be seen above, we will use 4 sorted set to store the information we need to answer the queries. We store the information related to the most viewed items per user inside a sorted set with a key like "MostViewed: token". Similarly, we store the information for most viewed products in general using another sorted set with key like "MostViewed". In the same way, we store the recently viewed products inside two sorted sets with keys "viewed:token", and "viewed". 

We will store the sku of the products that has been viewed with a score as a timestamp in case we want to answer the recently viewed queries. In case of the most viewed queries, we will store the sku of the products that has been viewed with a score as a number which represent how many times it has been viewed. The sorted sets will be as shown below:

![image](https://s3.amazonaws.com/b2cbucket/analytics+products.png)


Note that we are using the zremrangeByRank command to set a limit for the size of the sorted set so that it will keep only the top elements and won't grow forever.


After that we can answer the product related queries as shown below:

* what are the most 10 viewed products for the current user ?

````
    @GET
	@Path("/get10MostViewedItemsByUser/{token}")
	@Timed
	@ApiOperation(value = "get the recent 10 most viewed items per user ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "token wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get10MostViewedItemsByUser(
			@ApiParam(value = "token of the user", required = true) @PathParam("token") String token) {

		if (token == null) {
			final String shortReason = "token wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("MostViewed:" + token);
			Set<String> allRecentItems = this.jedisClient.zrange("MostViewed:"
					+ token, 0, sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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


* what are the most 20 viewed products in general ?

````
    @GET
	@Path("/get20MostViewedItems")
	@Timed
	@ApiOperation(value = "get the recent 20  most viewed items ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get20MostViewedItems() {

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("MostViewed:");
			Set<String> allRecentItems = this.jedisClient.zrange("MostViewed:",
					0, sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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

* what are the most 10 recently viewed products by a user ?

````
    @GET
	@Path("/get10RecentViewedItemsByUser/{token}")
	@Timed
	@ApiOperation(value = "get the recent 10 viewed items per user ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "token wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get10RecentViewedItemsByUser(
			@ApiParam(value = "token of the user", required = true) @PathParam("token") String token) {

		if (token == null) {
			final String shortReason = "token wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("viewed:" + token);
			Set<String> allRecentItems = this.jedisClient.zrange("viewed:"
					+ token, 0, sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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

* what are the most 20 recently viewed products in general ?

````
    @GET
	@Path("/get20RecentViewedItems")
	@Timed
	@ApiOperation(value = "get the recent 20  viewed items ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get20RecentViewedItems() {

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("viewed:");
			Set<String> allRecentItems = this.jedisClient.zrange("viewed:", 0,
					sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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


To answer the queries related to the cache performance, we would call the below API whenever a page is requested from the cache with a flag indicating whether the page was found or not in the cache.

````
    @POST
	@Path("/page_requested/{url}/hit/{hit}")
	@Timed
	@ApiOperation(value = "record that a page requested from cache and whether a cache miss or hit ", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "url or hit wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public boolean recordRequestPageFromCache(
			@ApiParam(value = "url of the requested page", required = true) @PathParam("url") String url,
			@ApiParam(value = "page found in cache or not", required = true) @PathParam("hit") String hit) {

		if (url == null || hit == null) {
			final String shortReason = "url or hit wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			// keep most requested page with hit response from cache
			if (hit.equals("true")) {
				this.jedisClient.zincrby("MostHitPages:", -1, url);
			} else {
				this.jedisClient.zincrby("MostMissPages:", -1, url);
			}

			// keep the last 100 pages in both sets
			this.jedisClient.zremrangeByRank("MostHitPages:", 0, -101);
			this.jedisClient.zremrangeByRank("MostMissPages:", 0, -101);

			return true;
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

Then we will answer the queries as show below:
	

* what are the most 100 requested pages that was found in the cache ?

````
    @GET
	@Path("/get100MostHitPagedFromCache")
	@Timed
	@ApiOperation(value = "get the 100 most hit pages from cache", notes = "Returns list of pages urls", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get100MostHitPagedFromCache() {

		try {

			ArrayList<String> listOfUrl = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("MostHitPages:");
			Set<String> allRecentItems = this.jedisClient.zrange(
					"MostHitPages:", 0, sortedSetSize - 1);

			String[] urlList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String url : urlList) {
				listOfUrl.add(url);
			}

			return listOfUrl;

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


* what are the most 100 requested pages that was not found in the cache ?

````
    @GET
	@Path("/get100MostMissedPagesFromCache")
	@Timed
	@ApiOperation(value = "get the 100 most missed pages from cache", notes = "Returns list of pages urls", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get100MostMissedPagesFromCache() {

		try {

			ArrayList<String> listOfUrl = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("MostMissPages:");
			Set<String> allRecentItems = this.jedisClient.zrange(
					"MostMissPages:", 0, sortedSetSize - 1);

			String[] urlList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String url : urlList) {
				listOfUrl.add(url);
			}

			return listOfUrl;

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

Similarly to answer the queries related to the cart. We will need to call the below APIs whenever a product was added or deleted from the cart.

A product added to a cart:

````
    @POST
	@Path("/added_to_cart/{sku}")
	@Timed
	@ApiOperation(value = "record that item is added to cart", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Sku wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public boolean recordItemAddedToCart(
			@ApiParam(value = "sku of the added to cart item", required = true) @PathParam("sku") String sku) {

		if (sku == null) {
			final String shortReason = "Sku wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			// keep the most products that are added to cart by all users
			this.jedisClient.zincrby("AddedToCart:", -1, sku);
			// just keep the 20 most added to cart products
			this.jedisClient.zremrangeByRank("AddedToCart:", 0, -21);

			return true;
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

A product is deleted from cart:

````
    @POST
	@Path("/deleted_from_cart/{sku}")
	@Timed
	@ApiOperation(value = "record that item is deleted from cart", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "Sku wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public boolean deletedItemAddedToCart(
			@ApiParam(value = "sku of the deleted from cart item", required = true) @PathParam("sku") String sku) {

		if (sku == null) {
			final String shortReason = "Sku wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			// keep the most products that are deleted from cart by all users
			this.jedisClient.zincrby("DeletedFromCart:", -1, sku);
			// just keep the 20 most deleted from cart products
			this.jedisClient.zremrangeByRank("DeletedFromCart:", 0, -21);

			return true;
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

Then we can answer the below queries easily as shown below:

* what are the most 20 products added to cart ?

````
    @GET
	@Path("/get20MostAddedToCartItems")
	@Timed
	@ApiOperation(value = "get the 20  most added to cart items ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get20MostAddedToCartItems() {

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("AddedToCart:");
			Set<String> allRecentItems = this.jedisClient.zrange(
					"AddedToCart:", 0, sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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

* what are the most 20 products removed from cart ?

````
    @GET
	@Path("/get20MostDeletedFromCartItems")
	@Timed
	@ApiOperation(value = "get the 20  most deleted from cart items ", notes = "Returns array of items sku", response = String.class, responseContainer = "list")
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public ArrayList<String> get20MostDeletedFromCartItems() {

		try {

			ArrayList<String> listOfSku = new ArrayList<String>();

			long sortedSetSize = this.jedisClient.zcard("DeletedFromCart:");
			Set<String> allRecentItems = this.jedisClient.zrange(
					"DeletedFromCart:", 0, sortedSetSize - 1);

			String[] skuList = allRecentItems.toArray(new String[allRecentItems
					.size()]);
			for (String sku : skuList) {
				listOfSku.add(sku);
			}

			return listOfSku;

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

Finally to answer queries related to log in, the below API needs to be called whenever a user logs in :

````
    @POST
	@Path("/logged_user/{token}")
	@Timed
	@ApiOperation(value = "record that a user has been logged in", notes = "Returns true or false", response = Boolean.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "token wasn't given!"),
			@ApiResponse(code = 500, message = "Coudln't access Redis") })
	public boolean recordUserLoggedIn(
			@ApiParam(value = "token of the logged in user", required = true) @PathParam("token") String token) {

		if (token == null) {
			final String shortReason = "token wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {

			// keep all logged in users during a day
			long timestamp = System.currentTimeMillis() / 1000;
			this.jedisClient.zincrby("login:", timestamp, token);

			// we don't want this set to grow for ever , so we record the top
			// 200000 recent logged in users (based on your website)
			this.jedisClient.zremrangeByRank("login:", 0, -200001);

			return true;
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

Then we can easily answer the below query:

* how many users are currently logged in since last one hour ?

````
    @GET
	@Path("/getCountLoggedInUserWithinLastHour")
	@Timed
	@ApiOperation(value = "get the count of logged in users within last hour", notes = "Returns integer count", response = Integer.class)
	@ApiResponses(value = { @ApiResponse(code = 500, message = "Coudln't access Redis") })
	public long getCountLoggedInUserWithinLastHour() {

		try {

			long count = 0;

			long currentTimeStamp = System.currentTimeMillis() / 1000;

			long lastHourTimeStamp = currentTimeStamp - 3600;

			count = this.jedisClient.zcount("login:", lastHourTimeStamp,
					currentTimeStamp);

			return count;

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
