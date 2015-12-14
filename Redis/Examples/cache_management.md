#### [back](example_main.md)

For many websites nowadays, large part of the pages are rarely changing. To improve the performance for the website, a caching mechanism needs to be used when generating web pages from server side. As a very fast in-memory database, Redis can be easily used as a cache service. There are many production ready cache implementations available which were build by using Redis. Also Redis can be configured to act only as a cache and support directly out of the box the popular cache eviction algorithms such as LRU and others. However in this example we are showing how to build the basic functionalities of a cache service using Redis. This service will support the below rest API:

![image](CacheManagement.png =700x70)


Using this API you can check if the page has been already cached (a cache hit) and return the cached content. Otherwise, in case of a cache miss we cache the page content for the upcoming requests. The implementation of this API is shown below:

````
    @GET
	@Path("/{pageRequestURL}")
	@Timed
	@ApiOperation(value = "get a cached request", notes = "Returns a cached request by url", response = Request.class)
	@ApiResponses(value = {
			@ApiResponse(code = 400, message = "pageRequestURL wasn't given!"),
			@ApiResponse(code = 500, message = "coudln't get from Redis !") })
	public Request getRequest(
			@ApiParam(value = "pageRequestURL", required = true) @PathParam("pageRequestURL") String pageRequestURL) {

		if (pageRequestURL == null) {
			final String shortReason = "pageRequestURL wasn't given!";
			Exception cause = new IllegalArgumentException(shortReason);
			throw new WebApplicationException(cause,
					javax.ws.rs.core.Response.Status.BAD_REQUEST);
		}

		try {
			// get the cached request page by url
			Request resultRequest = new Request();
			resultRequest.setPageRequestURL(pageRequestURL);

			String cachedPageKey = "cache:" + pageRequestURL.hashCode();
			// check if the request already cached
			String chachedPageContent = this.jedisClient.get(cachedPageKey);

			// if cache miss
			if (chachedPageContent == null) {
				String pageContent = getPageContent(pageRequestURL);
				this.jedisClient.setex(cachedPageKey, this.cacheValidityTime,
						pageContent);
				resultRequest.setPageContent(pageContent);
			} else // cache hit
			{
				resultRequest.setPageContent(chachedPageContent);
			}

			return resultRequest;
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

So we store the page contents inside a Redis String data structure and retrieve the contents using Redis get command. In case the page wasn't cached previously we use the useful Reids command setex to store the page contents and set an expiration time at the same time. 