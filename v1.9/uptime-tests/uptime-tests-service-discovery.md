+++
draft = false
title = "Uptime tests and service discovery"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "uptime"
    weight = -600
+++

It is possible to have uptime tests dynamically defined on hot reload by a service discovery system such as `etcd` or `consul`

This can be especially useful if:

- The circuit breaker pattern is used
- Underlying service IPs change frequently and dynamically (microservices)

To set up service discovery, use a similar configuration to Tyk's other Service Discovery integration:

	```
	"uptime_tests": {
	    "check_list": [],
	    "config": {
	      "recheck_wait": 12,
	      "service_discovery": {
	        "use_discovery_service": true,
	        "query_endpoint": "http://127.0.0.1:4001/v2/keys/uptimeTest",
	        "data_path": "node.value"
	      }
	    }
	},
	```
Uptime tests by service discovery will load initially from the endpoint, it will not re-poll the service until it detects an error, at which point it will schedule a reload of the endpoint data. If used in conjunction with upstream target service discovery it enables dynamic reconfiguring (and monitoring) of services.

The document that Tyk requires is a JSON string encoded version of the `check_list` parameter of the `uptime_tests` field, here is an example using the simplified form for etcd:

	curl -L http://127.0.0.1:4001/v2/keys/uptimeTest -XPUT -d value='[{"url": "http://domain.com:3000/"}]'

In this example we have only added one uptime test.

