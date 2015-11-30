
+++
draft = false
title = "Advanced Options"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -150
+++

The advanced options tab comprises all of the additional elements taht can be defined for an API that are not crusial for the sucessful launch of the API into your Tyk cluster.

### Cache options

The cache optiosn section enables you to set gloabl cache configurations for your API if you do not have them individually set per endpoint. The optiosn are:

- **Enable caching**: This must be enabled for any caching to work across your API
- **Enable upstream control**: Setting this option forces Tyk to only cache content that has been specifically requested to be cached by the upstream API, this is done via the headers in the response object. See below for details
- **Cache timeout**: The timeout of the cache in seconds
- **Global cache**: Set to tru efor aggressive caching of all safe requests

#### Upstream cache control

If your server responds with the headers below as part of the response object then Tyk will will cache the request for the limits defined. This can be very useful if you want to have dynamic caching of API requests but do not want the aggressive caching of the default Tyk configuration.

- `x-tyk-cache-action-set: 1` If Tyk finds this header, Tyk will cache the request
- `x-tyk-cache-action-set-ttl: 60` If tyk finds this header, it will overrride the TTL of the cached response, otehrwise it will default to the cache timeout defined in the main configuration

### Cross Origin Resource Sharing (CORS)

This section defines any CORS settings that you wish to enable for your API, enable the various header, content and security methods that you require for CORS to be enabled on your API.

### Batch Requests

To enable batch requests, simply enable this check box and a new public endpoint on your listen path called `{listen_path}/tyk/batch` will ebcome availbale to end-users on reload. For details about how to format requests to this endpoint please see the related Tyk Core documentation.

### Segment Tags (Node Segmentation / API Sharding)

Tyk supports node segmentation - this is when you have several clusters of nodes that serve distinct service types (e.g. Health, Finance, Stock), or geographically diverse services (e.g. Data-center-1, Data-center-2 etc.). You can give an APi any number of tags, if a node is configured to be segmented (self-hosted and hybrid only), then those nodes will *only* load the definitions with matching tag spaces.

Add the tags you wish to paint the API with here, non-segmented nodes will load the API regardless so long as it is active. Node segmentation is enabeld on a node-by-node basis, it is not configured via the dashboard.

### Whitelisted IP's

As an additional security precaution it is possible to limit inbound requests by IP, enable this option and type in a list of IP addresses to for Tyk to begin allowing those IP's through. 

The IP Addresses are strict, wildcards and subnet notation are not supported.

### Webhooks

It is possible to attach web hooks to events within the Tyk Dashboard (this is an interface to the core event handler functionality described in the tyk Core documentation). webhooks can be shared across API Configurations, adding Webhooks and how to configure them is described in the [webhooks section](../webhooks/) of this documentation.

To set a webhook, simply select an event to atach to:

- **Quota Exceeded:** If a keys quota has been exceeded
- **Rate limit Exceeded:** If a key is making too many requests and has been throttled
- **Authentication Failure:** If an unauthorised key has tried to gain access
- **Key Expired:** If an expired key has been used to attempt to gain access
- **Version Access Failure:** If a retiired version has been accessed
- **Breaker Tripped**: When a circuit breaker on a path trips and a service is taken offline
- **Breaker Reset**: When the circuit breaker comes back on-stream

Selecting a webhook to use requires you to create one first, please see the [webhooks section](../webhooks/) on how to do this.

Once selected, you can also set a cooldown period, a cooldown period is there to stop a "thundering herd" of vents from firing the same webhook (for the same key) over and over again. Setting this to a number of seconds means that once fired, Tyk will only fire the webhook again if it has been *x* secons since the last, same request.