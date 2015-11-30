+++
draft = false
title = "Uptime tests"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -200
+++

Tyk can capture uptime analytics data and test your underlying services with custom crafted HTTP requests. 

Use this section to enable the various options around up time testing.

### Use Uptime Tests with Target URL

If this option is set, the hostname of the request will be tested against a downtime list, if the host is "down", Tyk will try to use the next available host on the list - this should be used in conjunction with Round Robin load balancing

### Use Service Discovery

This option and it's related settings enable you to set uptime tests using a service discovery system such as etcd or consul instead of adding them to the API Definition.

The settings for this are very specific - please see the related section in the main Tyk Gateway Documentation for details on what these settings mean.

### Check URL's

This section lists out the URLs to test and enables you to add new test URLs.

#### Path

The URL to target, must include a full hostname (http://domain.com:port/test)

#### Method

The method to use with the request

#### Body 

Any body data to be sent to the endpoint 

#### Headers

Headers to use in the request

