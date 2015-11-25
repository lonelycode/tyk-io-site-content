+++
draft = false
title = "Caching"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_9]
    parent = "management"
+++

Tyk supports various ways of caching requests. At it simplest level, Tyk can cache all safe requests,
however you can also manually set exactly which endpoints patterns to cache, and if that doesn't suffice, or you
require more granular control, then you can enable upstream caching and have your application tell Tyk whether to cache
a request or not and for how long.

### Enabling Caching

To enable caching in your API, within your API definition you will need to set the `cache_options` flags in the main body of the definition:

	cache_options: {
	    cache_timeout: 10,
	    enable_cache: true,
	    cache_all_safe_requests: false,
	    enable_upstream_cache_control: false
  	}

### Caching *all* safe requests

If you set `cache_all_safe_requests` to true, then the cache will be global and all inbound requests will be evaluated by the caching middleware. This
is great for simple APIs, but for many more fine-grained control is required.

### Caching specific paths

To cache sonly specific endpoints, within the version data under the `extended_paths` section,
you will need to define the paths to cache in the `cache` list:

	extended_paths: {
		ignored: [],
		white_list: [],
		black_list: [],
		cache: [
			"widget",
			"badger",
			"fish"
		],
		transform: [],
		transform_headers: []
    }

Now Tyk will only cache the `/widget`, `/badger`, and `/fish` endpoints. Tyk will only cache safe requests, so `GET`, `OPTIONS` and `HEAD` requests. For many
this will suffice with regards to caching requests, however in some cases you may wish to have full control over when to cache and be reactive about
the time to live of the cached response.

### Upstream control

Upstream cache control enables you to set whether a response should be cached, and for how long. To enable this, you will need to set
`enable_cache` to and `enable_upstream_cache_control` to `true`.

Now you will also need to set on which paths to act, so add these paths to the `cache` list in the extended path section of your API version.

Tyk will evaluate the response headers sent from your application for these paths and based on the data in the response activate and set the cache
values.

The two response headers that Tyk looks for are:

1. `x-tyk-cache-action-set: 1` If Tyk finds this header, Tyk will cache the request
2. `x-tyk-cache-action-set-ttl` If Tyk finds this header, it will override the TTL of the cached response, otherwise it will default to `cache_options.cache_timeout`

Utilising this method gives the most control as it will also only cache requests based on their method, so if you only want `OPTIONS` requests
to be cached, then only that method/URL combination will be cached, ignoring other methods for the same path.
