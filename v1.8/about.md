+++
draft = false
title = "About Tyk"
date = 2014-12-30T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_8]
    weight = -310
home = "1"
+++

Tyk is a lightweight, open source API Gateway and Management platform that enables you to control who accesses your API, when they access it and how they access it. Tyk will also record detailed analytics on how your users are interacting with your API and when things go wrong.

***

## What is an API Gateway? ##

An API Gateway sits in front of your application(s) and manages the heavy lifting of authorisation, access control and throughput limiting to your services. Ideally, it should mean that you can focus on creating services instead of implementing management infrastructure. For example if you have written a really awesome web service that provides geolocation data for all the cats in My Account, and you want to make it public, integrating an API gateway is a faster, more secure route that writing your own authorisation middleware.

Tyk goes a step further in that it enables you to on-board developers through a dedicated portal, as well as analyse who uses your APIs and how they use it through a powerful analytics platform.

## Key Features of Tyk ##

Tyk offers powerful, yet lightweight features that allow fine gained control over your API ecosystem.

* **RESTful API** - Full programatic access to the internals makes it easy to manage your API users, keys and Api Configuration from within your systems
* **Multiple access protocols** - Out of the box, Tyk supports Token-based, HMAC, Basic Auth, OAuth2 and Keyless access methods
* **Rate Limiting** - Easily rate limit your API users, rate limiting is granular and can be applied on a per-key basis
* **Quotas** - Enforce usage quotas on users to manage capacity or charge for tiered access
* **Granular Access Control** - Grant API access on a version by version basis, grant keys access to multiple APIs or just a single version
* **Policies** - Create policies to apply to quotas, rate limits and access rights to block sets of keys
* **Path by path permissions** - For highly tiered systems, grant access to only specific endpoints and methods
* **Key Expiry** - Control how long keys are valid for
* **API Versioning** - API Versions can be easily set and deprecated at a specific time and date
* **Blacklist/Whitelist/Ignored endpoint access** - Enforce strict security models on a version-by-version basis to your access points
* **Analyitcs logging** - Record detailed usage data on who is using your APIs (raw data only)
* **Zero downtime restarts** - Tyk configurations can be altered dynamically and the service restarted without affecting any active request
* **Web hooks** - easily integrate notifications, and events into your existing infrastructure for better monitoring
* **IP White-listing** - Grant access to only machines that you specify
* **Batch requests** - Enable batch request processing for your API clients to minimise calls they need to make to your API, both synchronous and asynchronous supported.
* **Health checks** - Easily query your Tyk nodes to see how your APIs are performing
* **Mock APIs** - Create mock APIs easily using extended path management
* **API Blueprint support** - Import API Blueprints (JSON format) from Apiary to quickly get started
* **Swagger support** - Import swagger files to get started quickly from your existing documentation
* **Scriptable middleware** - Add your own custom middleware components before and after the Tyk middleware stack to pre- and post-process requests and integrate with your stack.
* **Scriptable event handlers** - Write you own event handlers in JavaScript to script actions against key API events
* **Request and Response transformations** - Use simple templates to transform body data and add or remove headers on-the-fly on inbound and outbound requests from your API
* **Caching** - Cache API responses by endpoint or globally with a simple caching layer, increasing the responsiveness of your API and lowering the load on your servers
* **Portal** - Tyk  offers a highly flexible portal solution for you to on-board developers (requires dashboard)
* **API Documentation** The new Tyk Portal supports both API Blueprint and Swagger documentation generation that is fully templateable.
* **Active Monitoring** Actively monitor tokens an organisations and set webhooks when they reach key quota milestones.
* **Virtual Endpoints** Like AWS Lambda functions, you can run JavaScript snippets on endpoints to handle complex service interactions such as request batching.
* **Focus on microservices** Use circuit breaker patterns, hard timeouts, and round-robin load balancing to get the most out of your services
* **Service Discovery Support** Using [Etcd](https://github.com/coreos/etcd) or [Eureka](https://github.com/Netflix/eureka)? If your service discovery platform exposes a rest endpoint for your microservices, Tyk can consume it and dynamically reconfigure itself

Tyk is written in Go, which makes it fast and easy to set up. It's only dependencies are Redis,
and a MongoDB database (**v2.6 or higher required for analytics**, optional).

## Why? ##

Tyk was built because other open source API Gateways in the market come with dependencies and bloat, attempting to be too many things to too many people. Tyk is flexible, focused, simple, and does one thing well - protecting your API from unauthorised access.
