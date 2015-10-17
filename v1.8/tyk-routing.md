+++
draft = false
title = "Tyk URL Structures"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_8]
    weight = -300
    
+++

It is often confusing to try and understand how tyk handles URL routing to grant access to upstream APIs, we've made sure that the way Tyk handles inbound requests is as flexible as possible in order to allow for as many styles of configuration as possible. Here are a few scenarios which explain how the gateway can be used to route traffic through the proxy.

Tyk uses something called a `listen_path` in its API Definitions to route requests to the appropriate upstream API, so if you had a microservice architecture with three api's (`api1.someserver.com`, `api2.anotherserver.com` and `pi3.yetanotherserver.com` lets call them), then Tyk could be set up to proxy those three apis in a few ways, here's a few scenarios:


## Option 1 - Raw Gateway

If you use the Tyk Gateway application on it's own - this means not using the host manager, and just letting tyk act as a proxy, assume tyk is running on `tyk.mygateway.com`, now out of the box it will do the following when you add those APIs to it's configuration, for conveniences sake, we are asuming you are seting the `listen_path` manually and not letting it be set by the dashboard (which would assign a UUID):

	http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*

Note the upstream API URL's can be arbitrary, so long as the base path is correct.

This scenario suits a miscroservice architecture well, since the listen paths are configurable you can set up a unified URL structure for your whole API group as a whole. Remember that API policies and keys can grant access to multiple upsream APIs with global rate limits, so one key can now be granted access to all three APIs.

## Option 2 - Use the host manager

The host manager is a utility application that can manage NGinX for you, it's sole purpose is to create and destroy nginx configuration (server) blocks and manage an nginx process. since tyk isn;t a web server, it doesn;t handle domain routing but with the host manager, domain routing and configuration can be plugged into the tyk stack and workflow. 

For example if you have many APIs and want to expose them seperately - the server blocks provide a way to map subdomains or directory locations to upstream, this is what Tyk does by default if you use our docker quickstart script, it would manage the above using `api_slug` values (the listen path here is set to a UUID to ensure there are no routing conflicts), the route now would look like:

	http://api.domain.com/* -> http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://api2.domain.com/* -> http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://api3.domain.com/* -> http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*

This may seem arbitrary, but Tyk gateway is not a web server, we think that routing, url rewrites, etc. should be left to software that do it better than we would, hence the host manager being slaved to NginX.

## Option 3 - Host Manager oganisation level blocks (v1.7)

In v1.7 (Dashboard 0.9.5) the host manager has been upgraded to allow per-organisation API server block manipulation, as in, instead of having one-server-block-per-api, we actually have one-server-block-per-organisation, and now you can have something like this:

	http://api.organisation-name.domain.com/api1/* -> http://tyk.mygateway.com/api1/* -> api1.someserver.com/*
	http://api.organisation-name.api.domain.com/api2/* -> http://tyk.mygateway.com/api2/* -> api2.anotherserver.com/*
	http://api.organisation-name.api.domain.com/ais3/* -> http://tyk.mygateway.com/api3/* -> api3.yetanotherserver.com/*

The power here comes from being able to manage many organisations (and their respecitve APIs) with a single installation, and since the nginx blocks are actually just templates that use the go tempalte syntax, they are entirely configurable to whatever set up your company has.
