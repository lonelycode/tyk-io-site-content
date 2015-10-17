+++
draft = false
title = "Tyk 1.8 - Connecting Everything"
image = "/imgs/tyk-1-8-released.png"
description = "Tyk 1.8 is out, our most innovative and performant release, yet, bringing all the goodies that you'd expect from a cutting-edge API Management platform"
date = 2015-08-26T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk", "v1.8"]
+++

Tyk v1.8 is a major evolution in the Tyk API Platform; this release has focused on key resilience patterns, improved performance and some key innovations unique to Tyk. It's been a longer phase between releases, and we've taken our time to integrate many features so they work well, together.

### API Sharding, or multi-data-center support

With this release we're introducing a new way to manage a large cluster of APIs that we're calling API sharding, the ability to segment your APIs out by region, sector, specialty or anything else you can think of and have dedicated clusters just for those services.

![Tyk 1.8 - API Sharding](/imgs/tyk-api-sharding.png)

Sharding enables you to configure a Tyk node to accept APIs that are assigned specific tags, allowing you to group APIs within specific VPCs, data centers or maybe just to assign to different load balancers. Sharded APIs share policy data, so keys that work across APIs will still be able to "cross boundaries" if their policy allows access.

API Segmentation is a powerful feature within Tyk, and lets you manage a whole fleet of segregated APIs from a single interface, and re-allocate those definitions from one node cluster to another with a simple change of tags. 

### Virtual Endpoints

Tyk now supports virtual endpoints, a slimmed-down, lighter form of AWS lambda functions. As of 1.8 you can inject short, consice JavaScript functions at the end of your URL paths to perform complex service interactions and return aggregated, custom responses back to your end user.

![Tyk 1.8 - Virtual Endpoints](/imgs/virtual-endpoint.png)

Imagine the scenario where you have multiple services that supply related data about a product. An API Consumer could request data for a product from all of those endpoints independently and merge them itself, or your API could present an easy, simple endpoint that handles the interactions with back-end systems (including potentially secure ones) and returning a custom data structure that is more useful to the consumer.

Going a step further, in order to not re-run this command, these virtual endpoints can be cached, making expensive operation easier and safer to expose.

Virtual endpoints are not limited to aggregations. They could be used for anything that requires a complex interaction with a back-end service, be that writing data and requesting it at the same time, or potentially even turning an API request that affects multiple upstream services into an atomic interaction.

### Resilience Patterns & Microservices

This release of Tyk features multiple optimizations for working with micro services, though these patterns are not limited to microservices and can be applied to any kind of API that has specific performance requirements. Lets start at the top...

#### Load Balancing

You can now assign multiple upstream hosts that Tyk will balance traffic to in a round-robin fashion. This is great if you have a cluster of containers all providing the same service and don’t want to put a load balancer between the services and the gateway.

#### Service discovery

Hand-in-hand with load balancing is our new service discovery module, this offers integrations for configuration management tools such as Consul, Etcd and Eureka. In fact, so long as your service discovery system provides an HTTP endpoint and JSON output, Tyk can use it to dynamically configure itself.

For example, imagine if you have multiple containers providing the same service, but occasionally the processes crash and your auto-recovery systems spin up new containers on new IP addresses. Ideally, your services will register themselves with your service discovery layer so they can be found by other systems.

Tyk will periodically request a new host, or host list from your service discovery module to ensure that it is proxying information to the correct servers / containers. This means that your gateway configuration can be environment aware on an API-by-API basis, self-healing and recovering from systemic failures without external intervention.

#### Circuit Breakers and Hard Timeouts

Going hand-in-hand with the new service discovery features are our new microservice patterns: the circuit breaker and the enforced timeout.

If you set an enforced timeout on a specific path, let's say it's a long-running API call, you can ensure service levels by forcing the request to return within a specific timeout. this can be very useful if a consuming client has not been written to close connections properly or hangs if the upstream service runs for too long.

If a hard timeout isn't enough, you can have Tyk automatically stop requests from reaching a failing service, take that service offline when a specific failure threshold has been set and then bring it back on-line only once your infrastructure has corrected itself.

These are the first step into ensuring that Tyk fits well with your microservice infrastructure, as well as providing patterns that can be used in non-microservice API environments to ensure resilience and reliability of upstream services.

### Richer Analytics

We've been thinking a lot about being able to harvest and segment data in our analytics system, and as part of this update, we've embedded a powerful new feature that lets you tag traffic as it comes through your nodes, allowing you to segment your analytics and view how your traffic is shaped by different nodes, clusters, policies or individual user-groups.

![Tyk 1.8 - Analytics Tagging](/imgs/analytics-tagging.png)

### Improved Performance

One thing we strive for with every release of Tyk is to push the performance threshold of Tyk up. With Tyk 1.8 we've reduced latency and memory footprint, as well as taken full advantage of the new low-latency garbage collection in Go 1.5.

As with every release, we like to put it through its paces in our test environment and share those results with you, so first off the Test setup:

- 1 x NginX server (4GB RAM / 4-cores) as our upstream API that we were proxying, this was serving a short JSON document
- 1 x Dashboard / Redis and Mongo server (2GB RAM / 2-cores) - this was mainly for configuration and the data store
- 1 x Tyk node (2GB RAM / 2-cores) this was our actual load target, testing the throughput of the node.

The test was a rush from 1 to 1000 users over 2 minutes. By the end of the test it was generating ~1000 requests per second.

**The results:**

These results showed strong, low-latency performance proxying ~1000 requests per second with latencies between 10ms and 50ms on a host that costs $0.03 per hour. 

![Tyk 1.8 - Load test results](/imgs/v1.8-performance.png)

We cranked the test up to 2000 users over 3 minutes, and got a choppier picture where latency peaked briefly at ~1,500 rps on the same hardware, but on average, this rush generated 166,782 successful hits in 180 seconds and the average hit rate of 926.57/second over the period of the test translates to about 80,055,360 hits/day with an average response time of 46ms (on the same, $0.03 per hour hardware).

Now we're not recommending you do this, but you can definitely get some serious bang for your buck with a well-configured Tyk node, the results of the ravaging:

![Tyk 1.8 - Large load test results](/imgs/tyk-destruction-test.png)

It's been a long release schedule, and we're incredibly happy with the results - we hope you are too. As always, if you have any questions, issues, requests or bribes, please let us know on our community mailing list, on github or just get in touch with me directly. --M

