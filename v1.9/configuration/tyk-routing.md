+++
draft = false
title = "Tyk Domains and URL handling"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_9]
    weight = -300
    parent = "configuration" 
+++

As of version 1.9 Tyk no longer requires the Host Manager to handle domain routing, or to run multiple services on the same port.

The Host manager will continue to work, and it is still configured the same way as previous versions. 

This document will outline how Tyk can be used to bind APIs to domain names using both internal domain handling and what URLs look like with the host manager.

### Raw Gateway

If you use the Tyk Gateway application on it's own - this means not using the host manager or binding domains to APIs, and just letting tyk act as a proxy, assume tyk is running on `tyk.mygateway.com`, now out of the box it will do the following when you add those APIs to it's configuration, for conveniences sake, we are asuming you are setting the `listen_path` manually and not letting it be set by the dashboard (which would assign a UUID):

	http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*

It is often confusing to try and understand how Tyk handles URL routing to grant access to upstream APIs, we've made sure that the way Tyk handles inbound requests is as flexible as possible in order to allow for as many styles of configuration as possible. Here are a few scenarios which explain how the gateway can be used to route traffic through the proxy.

Tyk uses something called a `listen_path` in its API Definitions to route requests to the appropriate upstream API, so if you had a microservice architecture with three api's (`api1.someserver.com`, `api2.anotherserver.com` and `api3.yetanotherserver.com` lets call them), then Tyk could be set up to proxy those three apis in a few ways, here's a few scenarios:

Note the upstream API URL's can be arbitrary, so long as the base path is correct.

This scenario suits a microservice architecture well, since the listen paths are configurable you can set up a unified URL structure for your whole API group as a whole. Remember that API policies and keys can grant access to multiple upstream APIs with global rate limits, so one key can now be granted access to all three APIs.

### Tyk Domains

Tyk has multiple levels of domain management that can be added to a nodes, these are bound to each API entry in the Tyk routing table:

1. Bind the gateway to a domain - by default Tyk will react to any inbound requests, if you set up a domain name for the host name, then this will form the basis of all API maps, this will take the form of:

	- gateway-domain/listen_path_1
	- gateway-domain/listen_path_2
	- gateway-domain/listen_path_3

2. Bind individual APIs to their own domains - you can add a domain field to your API definition, this will then have Tyk only route to domain + listen_path configuration of this set up, multiple APIs can use different listen_paths to be maped to the same domain:
	
	- api-domain-1/listen_path_1
	- api-domain-1/listen_path_2
	- api-domain-1/listen_path_3
	- api-domain-2/listen_path_1
	- api-domain-2/listen_path_2
	- gateway-domain/listen_path_1 (for unmapped APIs)

3. Bind the Tyk API to a segregated domain - This is an additional security feature, so in addition to the above, it is possible to set a domain name for the control API only.

4. Dashboard domain - it is possible to bind the dashboard to a specific domain

5. Portal domains - Any organisation that has their CNAME set (and CNAME Enabled) in their organisation object (see the Advanced Admin API) will bind the portal to that specific domain. 

### Host manager URL structures

The host manager is a utility application that can manage NGinX for you, it's sole purpose is to create and destroy nginx configuration (server) blocks and manage an nginx process. Tyk now supports domain routing natively, so thehost manager is only retained for legacy set ups.

If you have many APIs and want to expose them separately - server blocks provide a way to map subdomains or directory locations to upstream, this is what Tyk does by default if you use our docker quickstart script, it would manage the above using `api_slug` values (the listen path here is set to a UUID to ensure there are no routing conflicts), the route now would look like:

	http://api.domain.com/* -> http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://api2.domain.com/* -> http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://api3.domain.com/* -> http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*


#### Host Manager oganisation level blocks (v1.7+)

As of v1.7 (Dashboard 0.9.5) the host manager has been upgraded to allow per-organisation API server block manipulation, as in, instead of having one-server-block-per-api, we actually have one-server-block-per-organisation, and now you can have something like this:

	http://api.organisation-name.domain.com/api1/* -> http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://api.organisation-name.api.domain.com/api2/* -> http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://api.organisation-name.api.domain.com/ais3/* -> http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*

The power here comes from being able to manage many organisations (and their respective APIs) with a single installation, and since the nginx blocks are actually just templates that use the go template syntax, they are entirely configurable to whatever set up your company has.
