+++
draft = false
title = "Request Size Limiting"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_9]
    parent = "management"
+++

Tyk supports forcing request size limits on a global and on a per-endpoint level, Tyk will reject any request that is too big.

To set up this middleware, simply add a new section to the `extended_paths` block of your API Definition version called `size_limits` :

	"size_limits": [
	    {
	      "path": "widget/{id}",
	      "method": "PUT",
	      "size_limit": 25
	    }
	  ]

The size limit must be in in bytes.

### Global size limiting

To add a global check for size limits, simply add:

	`"global_size_limit": 500` 

To the version element of your API Definition, the global size limit will be checked before the specific path-based one.