+++
draft = false
title = "Virtual Endpoints"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Virtual paths are a powerful feature in Tyk, they enable you to create small code snippets that run on your set path. These snippets can be written in JavaScript and offer an easy way to create dynamic, flexible endpoints that perform complex interactions with your underlying services. Virtual endpoints are less-powerful versions of AWS Lambda functions as they do not offer a full NodeJS-like environment.

Virtual endpoints can also be cached, which can significantly reduce load on your back-end services if you are providing an aggregate virtual endpoint.

### Sample use cases

A sample use case for this might be aggregate functions that bring together related data from multiple services in your stack into a single object. 

Alternatively you could produce a dynamic response object that transforms or computes data in some way from upstream services.

### A sample Virtual endpoint function

The below code is the basic outline of a virtual endpoint function:

	```
	function sampleVirtual(request, session, config) {
		log("Virtual Test running")

		log("Request Body: ")
		log(request.Body)

		log("Session: ")
		log(session)

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

A virtual endpoint needs to have the above signature in order to work correctly, anything other than the above will produce `undefined` errors. In a virtual endpoint, you have full access to a the request object, the session object (if the virtual path is in an authenticated API), and the configuration parameters of the function - though this is rarely used.

### Writing an aggregate function

The most common use case foe a virtual endpoint is to provide some kind of aggregate function to your end users. Our JSVM exposes the batch-request methods to the JS API:

	```
	function batchTest(request, session, config) {
		// Set up a reponse object
		var response = {
			Body: ""
			Headers: {
				"test": "virtual-header-1", 
				"test-2": "virtual-header-2",
				"content-type": "application/json"
			},
			Code: 200
		}

		// Batch request
		var batch = {
		    "requests": [
		        {
		            "method": "GET",
		            "headers": {
		                "x-tyk-test": "1",
		                "x-tyk-version": "1.2",
		                "authorization": "1dbc83b9c431649d7698faa9797e2900f"
		            },
		            "body": "",
		            "relative_url": "http://httpbin.org/get"
		        },
		        {
		            "method": "GET",
		            "headers": {},
		            "body": "",
		            "relative_url": "http://httpbin.org/user-agent"
		        }
		    ],
		    "suppress_parallel_execution": false
		}

		log("[Virtual Test] Making Upstream Batch Request")
		var newBody = TykBatchRequest(JSON.stringify(batch))

		// We know that the requests return JSON in their body, lets flatten it
		var asJS = JSON.parse(newBody)
		for (var i in asJS) {
			asJS[i].body = JSON.parse(asJS[i].body)
		}

		// We need to send a string object back to Tyk to embed in the response
		response.Body = JSON.stringify(asJS)

		return TykJsResponse(response, session.meta_data)
		
	}
	log("Batch Test initialised")
	```

The Virtual Endpoint plugin has access to the same methods that the tyk plugins API has. Please see the relevant documentation in that section to see what methods are available.

### Request objects

The request object that is passed into a Virtual Endpoint look like this:

	type RequestObject struct {
		Headers map[string][]string
		Body    string
		URL     string
		Params  map[string][]string
	}

To access the properties, you will need to access the capitalised versions, e.g. `request.Headers`.

### Session Objects

The session State object is the full session object, these will be available as follows:

	{
	    "allowance": 999,
	    "rate": 1000,
	    "per": 60,
	    "expires": 0,
	    "quota_max": -1,
	    "quota_renews": 1406121006,
	    "quota_remaining": 0,
	    "quota_renewal_rate": 60,
	    "access_rights": {
	        "234a71b4c2274e5a57610fe48cdedf40": {
	            "api_name": "Versioned API",
	            "api_id": "234a71b4c2274e5a57610fe48cdedf40",
	            "versions": [
	                "v1"
	            ]
	        }
	    },
	    "org_id": "53ac07777cbb8c2d53000002",
	    "meta_data": {"key": "value", "key2": "value2"}
	}



**Note:** The JavaScript environment that these methods run in is a traditional ECMAScript environment and dos not offer the expressive power of something like nodeJS. These methods are meant to provide a functional interpreter for complex interactions with your underlying service that cannot be handled by one of the other middleware components.