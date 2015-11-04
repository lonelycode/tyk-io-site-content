+++
draft = false
title = "Key changes in v1.8"
date = 2015-08-01T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_8]
    weight = -300
home = "1"
+++


Tyk version 1.8 is a step change in how we are tackling API management, focussing on three core areas: Microservices, Reporting and Deployment.

## Microservices

In this release we have some very juicy new features geared specifically towards the API Gateway's role in a microservices-style environment, however many will find these features will also be of benefit in more traditional setups.

### Virtual endpoints

Virtual endpoints enable you to write a short snippet of JS that will get run every time the endpoint is hit, very similar to AWS Lambda functions (though not as powerful), these functions can perform typical integration tasks for a microservice infrastructure such as aggregating multiple endpoints into a single user-friendly facade.

For example, you may have a set of product detail services:

- `ProductReviews` - Provides reviews for a product ID
- `ProductStock` - Provides Stock level information for a product ID from your warehouse
- `ProductDescription` - The description of the product as described by the marketing team
- `ProductImages` - The product image gallery

A third-party consumer of this API, in order to provide an overview of the product, would need to make four calls to your services in order to present it all. which is quite the burden on the developer.

Using a Virtual path, you could write a short function that uses our built-in batch-requester to asynchronously call those endpoints and return a rational result, via our built-in cache.

Naturally this isn't the only use case, but it offers a glimpse into the kind of flexibility that can be built into an API managed by tyk 1.8's new features.

### Circuit breakers and hard timeouts

In high-availability systems, you will want to return a response back to the client as soon as possible, and return gracefully instead of timing out (or running endlessly) after a long-running process hangs. Hard timeouts do exactly that, they let you set a failure threshold for any request on a path bypath basis.

Sometimes though, you need to go a little further, and instead be able to just stop inbound requests full stop once you start detecting a pattern of failure. This is where the circuit-breaker comes in. Tyk's circuit breaker will measure the number of failure codes that are returned by your upstream service, and if a specific threshold is hit (e.g. 80% of responses are error codes), instead of letting the failure cascade across your infrastructure, you will want to be notified and stop any further damage.

Tyk's circuit breaker will take a service offline and return an error to all clients for a specific "downtime period", you can then set up a webhook to notify when a circuit is "tripped", allowing your infrastructure to heal itself, or for manual intervention by your DevOps team.

### Round-robin load balancing

In dockerized environments, you may have a cluster of containers running the same service, and you will want to pass traffic between them equally. You could do this by adding a load balancing layer to the cluster, or, you could just tell Tyk to do it for you. Tyk has a built in capability to load balance across a host of upstream services in order to distribute load effectively.

This is especially powerful with the circuit breaker pattern, as can be seen in our next section when we bring these features together under our new "service discovery" features.

### Service discovery

Any API managed by Tyk can now enable a "service discovery mode" where you can dynamically feed host information to a running gateway when your upstream configuration changes.

Tyk's service discovery module lets you set up an endpoint to call which must return a JSON object, and a namespace to query to identify the host and port of the upstream instance (or instances, if you are using load balancing) that should be loaded up when the node starts, and how often to refresh this list based on a cache timeout.

We've integrated this module with the circuit breaker and load balancers, so that if the breaker trips, Tyk will try to fetch a new set of service information when the service is brought back online.

Service discovery should work with any service that can provide a JSON REST endpoint to query.

## Reporting

On the reporting side of things we have made our analytics platform more powerful by adding a new "tagging" feature. You can now add arbitrary tags to nodes (more about that below), policies, or keys that will be fed into the API Analytics recorded by the dashboard.

This means that you will be able to segment your analytics by:

- Where the traffic originated from in your setup (which nodes are doing the most work)
- How policies of users behave across your traffic (e.g. Free Premium and Pro tier users)
- How individual / special keys are behaving (e.g. internal services)

YWhen you push a tag into a policy or a key, those changes are reflected instantly in your analytics. So it can also serve as a quick tool to diagnose behaviour ad-hoc.

## Deployment

Related to the tagging approach before, we mentioned that nodes themselves can be tagged. In this version of tyk it is possible to "shard" your APIs across node groups. So for example, say you had "Health", "Finance" and "Ops" sectors that grouped APIs and the nodes should not be aware of any other APIs in your setup, you can now have a single node only selectively load APIs by filtering them by their respective Tags.

In this case you set a tag on your API Definition in a new `tags: []` property and any node that has any of those tags in it's filter list will load those APIs.

This means you can now manage a multi-cluster, multi-noe, segregated API ecosystem with a single dashboard, which should make your DevOps team happy.

## The full Changelog for v1.8:

- Security option added for shared nodes: Set `disable_virtual_path_blobs=true` to stop virtual paths from loading blob fields
- Added session meta data variables to transform middleware:

	You can reference session metadata attached to a key in the header injector using:

	```
	$tyk_meta.KEY_NAME
	```

	And in the body transform template through:

	```
	._tyk_meta.KEYNAME
	```

	You must enable session parsing in the TemplateData of the body transform entry though by adding:

	```
	"enable_session": true
	```

	To the path entry

- Added CORS support, add a CORS section to your API definition:

	 ```
	 CORS: {
	    enable: false,
	    allowed_origins: [
	      "http://foo.com"
	    ]
	 },
	 ```

- Full CORS Options are:

	```
	CORS struct {
		Enable             bool     `bson:"enable" json:"enable"`
		AllowedOrigins     []string `bson:"allowed_origins" json:"allowed_origins"`
		AllowedMethods     []string `bson:"allowed_methods" json:"allowed_methods"`
		AllowedHeaders     []string `bson:"allowed_headers" json:"allowed_headers"`
		ExposedHeaders     []string `bson:"exposed_headers" json:"exposed_headers"`
		AllowCredentials   bool     `bson:"allow_credentials" json:"allow_credentials"`
		MaxAge             int      `bson:"max_age" json:"max_age"`
		OptionsPassthrough bool     `bson:"options_pasthrough" json:"options_pasthrough"`
		Debug              bool     `bson:"debug" json:"debug"`
	} `bson:"CORS" json:"CORS"`
	```

- Fixed cache bug
- When using node segments, tags will be transferred into analytics data as well as any token-level tags, so for example, you could tag each node independently, and then view the traffic that went through those nodes by ID or group them in aggregate
- You can now segment gateways that use a DB-backed configurations for example if you have APIs in different regions, or only wish to service a segment of your APIs (e.g. "Health APIs", "Finance APIs"). So you can have a centralised API registry using the dashboard, and then Tag APIs according to their segment(s), then configure your Tyk nodes to only load those API endpoints, so node 1 may only serve health APIs, while node 2 might serve a mixture and node 3 will serve only finance APIs. To enable, simply configure your node and add to `tyk.conf` and `host_manager.conf` (if using):

	"db_app_conf_options": {
        "node_is_segmented": false,
        "tags": ["test2"]
    }

- You will need to add a `tags: []` section to your API definition in the DB to enable this feature, or set it in the dashboard.
- Dynamic endpoints support response middleware
- Dynamic endpoints support caching
- Dynamic endpoints also count towards analytics
- JSVM now has access to a `TykBatchRequest` function to make batch requests in virtual paths. Use case: Create a virtual endpoint that interacts with multiple upstream APIs, gathers the data, processes the aggregates somehow and returns them as a single body. This can then be cached to save on load.
- Added virtual path support, you can now have a JS Function respond to a request, makes mocking MUCh more flexible, TODO: expose batch methods to JSVM. To activate, add to extended paths:

	```
	virtual: [
        {
          response_function_name: "thisTest",
          function_source_type: "file",
          function_source_uri: "middleware/testVirtual.js",
          path: "virtualtest",
          method: "GET",
          use_session: true
        }
    ]
    ```

- Virtual endpoint functions are pretty clean:

	```
	function thisTest(request, session, config) {
		log("Virtual Test running")

		log("Request Body: ")
		log(request.Body)

		log("Session: ")
		log(session)

		log("Config:")
		log(config)

		log("param-1:")
		log(request.Params["param1"])

		var responseObject = {
			Body: "THIS IS A  VIRTUAL RESPONSE"
			Headers: {
				"test": "virtual",
				"test-2": "virtual"
			},
			Code: 200
		}

		return TykJsResponse(responseObject, session.meta_data)

	}
	log("Virtual Test initialised")
	```

- Added refresh tests for OAuth
- URL Rewrite in place, you can specify URLs to rewrite in the `extended_paths` section f the API Definition like so:

	```
	"url_rewrites": [
        {
          "path": "virtual/{wildcard1}/{wildcard2}",
          "method": "GET",
          "match_pattern": "virtual/(.*)/(\d+)",
          "rewrite_to": "new-path/id/$2/something/$1"
        }
      ]
    ```

- You can now add a `"tags":["tag1, "tag2", tag3"]` field to token and policy definitions, these tags are transferred through to the analytics record when recorded. They will also be available to dynamic middleware. This means there is more flexibility with key ownership and reporting by segment.`
- Cleaned up server output, use `--debug` to see more detailed debug data. Keeps log size down
- TCP Errors now actually raise an error
- Added circuit breaker as a path-based option. To enable, add a new section to your versions `extended_paths` list:

	```
	circuit_breakers: [
        {
          path: "get",
          method: "GET",
          threshold_percent: 0.5,
          samples: 5,
          return_to_service_after: 60
        }
      ]
     ```

- Circuit breakers are individual on a single host, they do not centralise or pool back-end data, this is for speed. This means that in a load balanced environment where multiple Tyk nodes are used, some traffic can spill through as other nodes reach the sampling rate limit. This is for pure speed, adding a redis counter layer or data-store on every request to a service would just add latency.

- Circuit breakers use a thresh-old-breaker pattern, so of sample size x if y% requests fail, trip the breaker.

- The circuit breaker works across hosts (i.e. if you have multiple targets for an API, the sample is across *all* upstream requests)

- When a circuit breaker trips, it will fire and event: `BreakerTriggered` which you can define actions for in the `event_handlers` section:

	```
	event_handlers: {
	    events: {
	      BreakerTriggered: [
	        {
	          handler_name: "eh_log_handler",
	          handler_meta: {
	            prefix: "LOG-HANDLER-PREFIX"
	          }
	        },
	        {
	          handler_name: "eh_web_hook_handler",
	          handler_meta: {
	            method: "POST",
	            target_path: "http://posttestserver.com/post.php?dir=tyk-event-test",
	            template_path: "templates/breaker_webhook.json",
	            header_map: {
	              "X-Tyk-Test-Header": "Tyk v1.BANANA"
	            },
	            event_timeout: 10
	          }
	        }
	      ]
	    }
	  },
	```

- Status codes are:

	```
	// BreakerTripped is sent when a breaker trips
	BreakerTripped = 0

	// BreakerReset is sent when a breaker resets
	BreakerReset = 1
	```

- Added round-robin load balancing support, to enable, set up in the API Definition under the `proxy` section:

	```
	...
	"enable_load_balancing": true,
	"target_list": [
		"http://server1",
		"http://server2",
		"http://server3"
	],
	...
	```

- Added REST-based Service discovery for both single and load balanced entries (tested with etcd, but anything that returns JSON should work), to enable add a service discovery section to your Proxy section:

	```
	// Solo
	service_discovery : {
      use_discovery_service: true,
      query_endpoint: "http://127.0.0.1:4001/v2/keys/services/single",
      use_nested_query: true,
      parent_data_path: "node.value",
      data_path: "hostname",
      port_data_path: "port",
      use_target_list: false,
      cache_timeout: 10
    },


	// With LB
	"enable_load_balancing": true,
	service_discovery: {
      use_discovery_service: true,
      query_endpoint: "http://127.0.0.1:4001/v2/keys/services/multiobj",
      use_nested_query: true,
      parent_data_path: "node.value",
      data_path: "array.hostname",
      port_data_path: "array.port",
      use_target_list: true,
      cache_timeout: 10
    },
    ```

- For service discovery, multiple assumptions are made:
	- The response data is in JSON
	- The response data can have a nested value set that will be an encoded JSON string, e.g. from etcd:

	```
	$ curl -L http://127.0.0.1:4001/v2/keys/services/solo

	{
	    "action": "get",
	    "node": {
	        "key": "/services/single",
	        "value": "{\"hostname\": \"http://httpbin.org\", \"port\": \"80\"}",
	        "modifiedIndex": 6,
	        "createdIndex": 6
	    }
	}
	```

	```
	$ curl -L http://127.0.0.1:4001/v2/keys/services/multiobj

	{
	    "action": "get",
	    "node": {
	        "key": "/services/multiobj",
	        "value": "{\"array\":[{\"hostname\": \"http://httpbin.org\", \"port\": \"80\"},{\"hostname\": \"http://httpbin.org\", \"port\": \"80\"}]}",
	        "modifiedIndex": 9,
	        "createdIndex": 9
	    }
	}
	```

	Here the key value is actually an encoded JSON string, which needs to be decoded separately to get to the data.

	- In some cases port data will be separate from host data, if you specify a `port_data_path`, the values will be zipped together and concatenated into a valid proxy string.
	- If use_target_list is enabled, then enable_load_balancing must also be enabled, as Tyk will treat the list as a target list.
	- The nested data object in a service registry key MUST be a JSON Object, **not just an Array**.


- Fixed bug where version parameter on POST requests would empty request body, streamlined request copies in general.
- it is now possible to use JSVM middleware on Open (Keyless) APIs
- It is now possible to configure the timeout parameters around the http server in the tyk.conf file:

	```
	"http_server_options": {
        "override_defaults": true,
        "read_timeout": 10,
        "write_timeout": 10
    }
    ```

 - It is now possible to set hard timeouts on a path-by-path basis, e.g. if you have a long-running microservice, but do not want to hold up a dependent client should a query take too long, you can enforce a timeout for that path so the requesting client is not held up forever (or manage it's own timeout). To do so, add this to the extended_paths section of your API definition:

 	```
	 ...
	 extended_paths: {
          ...
          transform_response_headers: [],
          hard_timeouts: [
            {
              path: "delay/5",
              method: "GET",
              timeout: 3
            }
          ]
    }
    ...
    ```
