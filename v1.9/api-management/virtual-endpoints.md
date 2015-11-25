+++
draft = false
title = "Virtual Endpoints"
date = 2014-07-29T10:57:08Z
[menu.sidebar_v1_9]
    parent = "management"
    weight = -100
+++

Virtual endpoints are new and unique to Tyk. with a virtual endpoint, you can plug short JavaScript functions at the end of a Tyk route and have them run when the endpoint is called.

A sample use case for this might be aggregate functions that bring together related data from multiple services in your stack into a single object.

Alternatively you could produce a dynamic response object that transforms or computes data in some way from upstream services.

**Note:** The JavaScript environment that these methods run in is a traditional ECMAScript environment and dos not offer the expressive power of something like nodeJS. These methods are meant to provide a functional interpreter before complex interactions with your underlying service that cannot be handled by one of the other middleware components.

### Virtual Endpoint Functions

To create one of these methods, create a file and place it in a subdirectory of the Tyk configuration environment (ideally under the `middleware` folder in your Tyk installation), they are pretty clean, here is a sample method:

	```
	function sampleVirtual(request, session, config) {
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

The JSVM that this method runs in is the same as the plugins and middleware API, so you will have access to the same methods that this does.

#### An aggregate JS function

The most common use case for this functionality, as we see it, is to provide some form of aggregate data to your users, here's a snippet that will do just that using the new Batch processing API:

	```
	function batchTest(request, session, config) {
		// Set up a response object
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

The above code is pretty self explanatory, so we won't go into great detail - the batch object here is the same object that is fed into our Batch Request method that is exposed as part of certain API definitions.

### Adding Virtual Endopints to your API Definition

Virtual paths follow the same layout and setup as other elements in the `extended_path` section of the API definition:

	```
	...
	virtual: [
        {
          response_function_name: "batchTest",
          function_source_type: "file",
          function_source_uri: "middleware/testVirtual.js",
          path: "get-batch",
          method: "GET",
          use_session: true
        }
    ]
    ```

#### `response_function_name`

This is the function to run, we need to know this as it will be the entrypoint into your code, this will be called first. Make sure it is unique, all plugins run in the same VM, so if there are naming collisions you may end up with unpredictable behaviour.

#### `function_source_type`

This can be `file` or `blob` If set to `file`, then Tyk will pre-load the JS from disk, if set to blob, then Tyk will base64-decode a string from the `function_source_uri` section.

#### `function_source_uri`

This will be the relative path to the source of the functionality (e.g. `myfile.js`), or a blob of base64-encoded data that represents the same information. Blob mode is mainly used by the dashboard to make code injection easier on multiple node deployments.

#### `use_session`

If true then the key session data will be provided to the function as the `session` variable. See the plugins documentation for more detail about this object.
