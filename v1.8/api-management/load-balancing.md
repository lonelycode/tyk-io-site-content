+++
draft = false
title = "Load Balancing"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_8]
    parent = "management"
+++

As of v1.8, Tyk supports naive round-robin load-balancing in it's proxy. This means that Tyk will rotate requests through a list of target hosts as requests come in. This can be very useful in microservice architectures where clusters of specialised services are launched for high availability.

Setting up load balancing is done on an API by API basis, and is defined in the API Definition file/object:

### `proxy.enable_load_balancing`

Set this value to true to have a Tyk node distribute traffic across a list of servers. **Required: ** You must fill in the `target_list` section.

### `proxy.target_list`

A list of upstream targets (can be one or many hosts):

	```
	"target_list": [
		"http://10.0.0.1",
		"http://10.0.0.1",
		"http://10.0.0.1",
		"http://10.0.0.1"
	]
	```

See the section on Service Discovery to see how you can integrate a service discovery system such as Consul or Etcd with Tyk and enable dynamic load balancing support.
