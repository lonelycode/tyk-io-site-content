+++
draft = false
title = "Creating an IP-based rate-limiter with Tyk and JavaScript middleware"
image = "/imgs/ip-rate-limit.jpg"
description = "We've recently had a user ask if it was possible to have Tyk act as a rate limiter based on IP address instead of by token, this isn't possible out of the box, but it is with our Middleware API and some simple JS"
date = 2015-06-10T09:30:00Z
author = "Martin Buhr"
categories = ["Tyk", "Tyk Dashboard", "Middleware"]
+++


We've recently had a user ask if it was possible to have Tyk act as a rate limiter based on IP address instead of by token, this isn't possible out of the box, but it is with our Middleware API and some simple JS

So, you may ask yourself how we achieve this feat? It's actually really straightforward. First off, we'll need to set up our API in a very specific way, we'll use a file-based definition here as we're assuming this is a simple deployment of Tyk.

The way we will do this is by creating a custom JS middleware function that will run before any other Tyk processing takes plae, this includes all rate limiting.

The middleware will :

1. Catch the request
2. Extract the IP address from the header
3. Create a key based on the IP address with a specific rate limit
4. Inject the IP address as the authorization header for Tyk to use

This means that rate limits are on a per-IP basis, which is great, and also means that Tyk can just work through the request normally once the middleware processing is done.

We will start off by defining our API appropriately, we basically want an API that will invoke the middleware as a pre-processor, and then also ensure that any keys that are created do not last forever.

The Tyk API Definition will look something like this:

	{
		"name": "Tyk Test API",
		"slug": "test-name",
		"api_id": "ip-ratelimit-api",
		"org_id": "1",
		"use_keyless": false,
		"use_oauth2": false,
		"auth": {
		    "auth_header_name": "x-tyk-authorization"
		},
		"definition": {
		    "location": "header",
		    "key": "version"
		},
		"version_data": {
		    not_versioned: true,
		    versions: {
		        "Default": {
		            "name": "Default",
		            "expires": "3011-02-02 00:00",
		            "use_extended_paths": true,
		            "extended_paths": {}
		        }
		    }
		},
		"proxy": {
		    "listen_path": "/b605a6f03cc14f8b74665452c263bf19/",
		    "target_url": "http://httpbin.org",
		    "strip_listen_path": true
		},
		"custom_middleware": {
		    "pre": [
		        {
		            "name": "ipRateLimiter",
		            "path": "middleware/ipRateLimiter.js",
		            "require_session": false
		        }
		    ]
		},
		"session_lifetime": 172800,
		"active": true
	}

The key things of note here are the `custom_middleware` section, here we've defined where to find our files and what the objects are called. Next up is the `session_lifetime` value, this sets a default key lifetime for every key created on this API, in this case we've set it to 48 hours.

Now that we are ready with the actual API, we can set up the middleware, it's actually quite short - this is the entire IP rate limiter:

	// ---- A Middleware based IP Rate limiter -----
	var ipRateLimiter = new TykJS.TykMiddleware.NewMiddleware({});

	ipRateLimiter.NewProcessRequest(function(request) {
	    // Get the IP address
	    var thisIP = request.Headers["X-Real-Ip"][0];

	    // Set auth header
	    request.SetHeaders["x-tyk-authorization"] = thisIP;

	    var keyDetails = {
	        "allowance": 100,
	        "rate": 100,
	        "per": 1,
	        "expires": 0,
	        "quota_max": 100,
	        "quota_renews": 1406121006,
	        "quota_remaining": 100,
	        "quota_renewal_rate": 60,
	        "access_rights": {
	            "ip-ratelimit-api": {
	                "api_name": "Test API",
	                "api_id": "ip-ratelimit-api",
	                "versions": [
	                    "Default"
	                ]
	            }
	        },
	        "org_id": "53ac07777cbb8c2d53000002"
	    }

	    TykSetKeyData(thisIP, JSON.stringify(keyDetails), 1)
	    
	    return ipRateLimiter.ReturnData(request);
	});

	// Ensure init with a post-declaration log message
	log("IP rate limiter JS initialised");

The above code should go into a file called `ipRateLimiter.js` in the `middleware/` folder of your Tyk istallation.

If you start Tyk now, the IP rate limiter will be in place. 

What is actually going on here? It's exactly as we said above, we extract the IP address, here we are assuming that `"X-Real-Ip"` is a real header, since we are using NGinX to manage our domain, we have set a rule in the server block to make sure that the IP address is included as a header on each request:

	location / {
        rewrite /(.*) /b605a6f03cc14f8b74665452c263bf19/$1 break;

        proxy_pass_header Server;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_pass http://tyk;
    } 

The thing of note here is `proxy_set_header X-Real-IP $remote_addr;`, this is imortant, as otherwise you won't have access tot he IP address from the header.

Once we have the IP address, we set this as the authorisation header for the request.

In the next section we have a template for a session object, here we are setting the rate limits manually, we could also use a policy file to do this and just attach the policy ID. In this case we have set the rate limit to 100 requests per second with an overall quota of 100 per minute.

We now use the built-in method `TykSetKeyData` to create the key, notice the last input to this funciton is `1`, this forces Tyk to not reset the quota for this transaction, which is the default behaviour - we don't want Tyk re-setting the quotas and rate limiters every time the IP shows up, we just want to ensure the key is present. 

When the middleware completes, Tyk processes the now-modified request as if it had an authorisation token (in this case the IP address) and the rate limits are enforced.

And that's it, this key will last for 48 hours, at which point it will be re-created. This means that your Redis instance won't get flooded with infinite IP addresses as traffic grows.

Enjoy! :-)

