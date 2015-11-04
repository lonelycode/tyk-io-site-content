+++
draft = false
title = "Circuit Breakers"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_8]
    parent = "management"
+++

Tyk has a built-in circuit breaker pattern as a path-based option. Our circuit breaker is threshold-based, so if x% of requests are failing then the circuit is tripped. When the circuit is tripped, then the gateway will stop _all_ inbound requests to that service for a pre-defined period of time (a recovery time-period).

The circuit breaker will also emit an event which you can hook into to perform some corrective or logging action.

To enable The breaker, you will need to add a new section to your versions `extended_paths` list:

	```
	"circuit_breakers": [
        {
          "path": "get",
          "method": "GET",
          "threshold_percent": 0.5,
          "samples": 5,
          "return_to_service_after": 60
        }
      ]
     ```

Circuit breakers use a threshhold-breaker pattern, so out of sample size `x` if `y%` of requests fail, the breaker will trip. When the breaker trips, the service is taken offline for the `return_to_service_after` period and an event is triggered.

**Note:** The circuit breaker works across hosts (i.e. if you have multiple targets for an API, the sample is across *all* upstream requests)

**Note:** Circuit breakers are individual on a single host, they do not centralise or pool back-end data, this is for speed purposes. This means that in a load balanced environment where multiple Tyk nodes are used, some traffic can spill through as other nodes reach the sampling rate limit.

## Events

When a circuit breaker trips, it will fire and event: `BreakerTriggered` which you can define actions for in the `event_handlers` section:

	```
	event_handlers: {
	    events: {
	      BreakerTriggered: [
	        {
	          handler_name: "eh_web_hook_handler",
	          handler_meta: {
	            method: "POST",
	            target_path: "http://posttestserver.com/post.php?dir=tyk-breaker",
	            template_path: "templates/breaker_webhook.json",
	            header_map: {
	              "X-Tyk-Test-Header": "Tyk v1.BANANA"
	            },
	            event_timeout: 10
	          }
	        }
	      ]
	    }
	  },
	```

The status codes returned to the template are:

	```
	// BreakerTripped is sent when a breaker trips
	BreakerTripped = 0

	// BreakerReset is sent when a breaker resets
	BreakerReset = 1
	```

## Service discovery

If you are using the service discovery module, every time the breaker trips, Tyk will attempt to refresh the node list.
