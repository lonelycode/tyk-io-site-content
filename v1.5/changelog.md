+++
draft = false
title = "Key changes in v1.5"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_5]
    weight = -300
home = "1"
+++

## Changes in Tyk v1.5

Version 1.5 has two major new features: Request modification on the fly and caching. Request modification enables you to change the inbound data objects in a request body on-th-fly into objects 
that your API can understand, this is particularly useful if you are mapping an XML-based API but want to accept JSON input. The request transforms middleware offered in v1.5 enables you to easily
modify inbound dJSON data. XML is not currently supported, but will be in a later release.

To make the transformation middleware even more powerful, we have added a new header management system that allows you to delete and add headers to requests on a per-endpoint basis, this means if you 
are exposing an API that only has a single authorisation key, you can now easily grant multi-user access to a single-user API without modifying your authentication layer and obfuscating authentication keys that are used internally.

While this was previously possible by using the scriptable middleware, these new middleware components significantly increase the performance of these often-required pieces of functionality.

We have also introduced a caching layer that can be activated on a per-endpoint basis or globally. The cache layer in tyk will store response data in Redis to a time limit you specify. Tyk will
only cache "safe" requests, such as GET, OPTIONS and HEAD requests. Caching can significantly increase the responsiveness of your API and lower the load on your servers.


## Changes to Tyk Dashboard (v0.9.3)

The dashboard has one major new feature: We have replaced the traditional "classic" APi-editor with a new, more intuitive API designer, this lets you easily manage versions, caching, transforms in
a clear and unified interface that is much easier to get started with than the old interface. This interface is still in Beta so we encourage our users to report bugs!

The second major announcement for the new dashboard is that we have now completely removed licensing from our software, this means that the dashboard, and the advanced management APi and the hot-reload 
manager are now completely free! For more details on how this affects License holders please see our blog post on the subject.

## The full change log for version 1.5 is:

- Added caching middleware
- Added optimisation settings for out-of-thread session updates and redis idle pool connections
- Added cache option to cache safe requests, means individual paths need not be defined, but all GET, OPTIONS and HEAD requests will be cached
- Added request transformation middleware, thus far only tested with JSON input. Add a golanfg template to the extended path config like so:

        "transform": [
            {
                "path": "/",
                "template_data": {
                    "template_mode": "file",
                    "template_source": "./templates/transform_test.tmpl"
                }
            }
        ]

- Added header transformation middleware, simple implementation, but will delte and add headers before request is outbound:

        "transform_headers": [
            {
                "delete_headers": ["Content-Type", "authorization"],
                "add_headers": {"x-tyk-test-inject": "new-value"},
                "path": "/post"
            }
        ]

- Clock skew for HMAC requests is now configurable
- Event handlers now also receive an encoded version of the inbound request as a base64-encoded string.

You can download the latest version of Tyk from our downloads page.
