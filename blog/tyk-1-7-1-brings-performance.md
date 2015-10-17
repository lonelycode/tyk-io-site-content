+++
draft = false
title = "Delivering performance with version 1.7.1"
image = "/imgs/tyk-1-7-1-released.png"
description = "We get compared to other API gateways a lot, and we haven't published any benchmarks recently... so we thought we'd do a major optimisation drive and make sure that Tyk was really competitive and really performant, so we've released version 1.7.1 of the gateway, and it flies."
date = 2015-07-01T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk"]
+++

We get compared to other API gateways a lot, and we haven't published any benchmarks recently, so we thought we'd do a major optimisation drive and make sure that Tyk was really competitive and really performant, so we've released version 1.7.1 of the gateway, and it flies.

As usualy you can get v1.7.1 from our github releases page. It's fully ocompatible with the latest dashboard (0.9.5), so you can literally do a drop-in replacement of the binary to get the improvements. Nothing else is needed.

We say there's performance improvements, so what are they? Lets look back, in the last round of benchmarks we did, we found that tyk could handle about 400 requests per second before getting sweaty, these were done using various tools (Gattling and LoadRunner on various hardware, including a local VM!).

With version 1.7 we found that you could push that a bit higher to around 600rps with some smaller optimisations where we offloaded non-returning write operations off the main thread into goroutines.

Now with 1.7.1 we've made Tyk work *well* at **over 1000 rps** on **cheaper** hardware.

### Setup

In our earlier benchmarks we used 4-core machines since Tyk is CPU bound, so the more cores, the better. But 4 cores are pricey and you don't want to be running a fleet of them. So the benchmarks presented below were set up on a much smaller 2-core machine. You can find the full test setup detail at the bottom of this blog post.

In brief, the test setup consisted of three Digital Ocean servers in their $0.03 p/h price bracket, these are 2GB, 2-Core Ubuntu boxes, these suckers are cheaper than the dual core offerings at AWS, which is why we picked them (AWS t2.medium instances are actually pretty competitive, but their burstable CPU capacity actually causes locks when it kicks in and they are still 20 cents more expensive at $0.052 per hour, so they aren't great for High Availability tests).

The test was performed using [blitz.io](http://blitz.io), in "rush" mode from 0 to 2000 concurrent connections. That totalled at **about 1,890 requests per second at the end of the test**.

### Results

![Tyk 1.7.1 performance benchmarks](/imgs/tyk-1-7-1-performance.png "Tyk 1.7.1 performance benchmarks")

![Tyk 1.7.1 performance benchmarks](/imgs/blitz-2.png "Tyk 1.7.1 performance benchmarks")

The average latency of 20ms is down to the load generators operating out of Virginia (AWS) and the Digital Ocean servers living in New York. When we ran comparison benchmarks using AWS instances, we found the latency was around 6ms, so the overhead is network latency, not software generated.

As can be seen from the results, Tyk 1.7.1 performed well with an average 28ms latency overall, serving up **115,077 requests in 120 seconds** with an average of **929 requests per second**, that translates to about **82,855,440 hits/day**

This is on a single node that costs **3 cents per hour**. We're pretty chuffed with that. We can see there's a few errors and timeouts, but at a negligible level compared to the traffic volume hitting the machine.

This release marks a major performance and stability boost to the Tyk gateway which we're incredibly proud to share with our users.

As always, get in touh with us in the comments or in our community portal.

### Test Setup In Detail

The test setup involved three 2GB/2Core 40GB Digital Ocean ubuntu 14.04 instances and configured for high network traffic by increasing these limits:

```
Add fs.file-max=80000 to /etc/sysctl.conf
```

Added the following lines to /etc/security/limits.conf

```
*          soft     nproc          80000
*          hard     nproc          80000
*          soft     nofile         80000
*          hard     nofile         80000
```

Tyk gateway was installed on one server, Redis and MongoDB on another and Nginx on the third. Each was left with the bare bones setup except to ensure that they bound to a public interface so we could access them from outside.

Tyk was configured with the redis and mongoDB details and had the redis connection pool set to 2500, with the `optimisations_use_async_session_write` option enabled. Analytics were set to purge every 5 seconds. Tyk was simply acting as a proxy so the API definition was keyless.

Redis and NginX were left as is, however Digital Ocean have their own configurations for these so they are already optmised with recommended HA settings. If you are using vanilla repositories, then you may need to configure both Redis and NginX to handle more connections by default for a rush test.


