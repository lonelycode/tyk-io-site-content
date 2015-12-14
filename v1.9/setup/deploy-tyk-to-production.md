+++
draft = false
title = "Deployment to Production"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -320
+++

So you want to deploy Tyk to production?

There's a few things worth noting that can ensure your the performance of your Tyk Gateway nodes. Here's some of the basic things we do for load testing to make sure machines don't run out of resources.

### What to expect

Tyk is pretty high performance, with the optimisations below, we can get a single 2-core/2GB Digital Ocean Gateway node to handle about 1,600 requests per second with latency under 30 seconds before things get messy:

![Tyk 1.9 performance](/assets/img/v1.9.load-test.png "Tyk 1.9 performance benchmarks")

*(This test was produced with [Blitz.io](http://blitz.io) with a test running form `0` to `2000` users in `2` minutes with an Open API)*

#### Pro tip: Keyspace operations and load testing

When Tyk rate-limits an access token, it does this in Redis in order to make the operation global across all running nodes (so simultaneous requests that are hitting two nodes have access to the same limiter), this means that if you load test with a single token, you will have resource contention on the rate limiter (as in, a single key will probably top out at 500-700 requests per second).

Testing with a larger key space (10 or so), will yield more production-style results as the keyspace operations will no longer be limited to a single rate limiter.

Now onto the actual advice on how to tweak a production installation...

### Split out your DB

This is a no-brainer, but keep Redis and Mongo off the system running the gateway, they both use lots of RAM, and with Redis and the Gateway constantly communicating you will be facing resource contention on the CPU for a marginal decrease in latency.

So in our setup, we recommend that Redis and Mongo live on their own systems, separate from your tyk gateway. If you like, run them together on the same box, that's up to you.

The network topology we like to use is:

- Two Tyk Gateway nodes (non purging) (load balanced)
- A separate mongoDB cluster
- A separate Redis server with fail-over or cluster
- One Tyk dashboard node with it's own local gateway process, this gateway process has purging enabled and is not sharded

### Make srue you have enough Redis connections

Tyk makes heavy use of Redis in order to provide a fast and reliable service, in order to do so effectively, it keeps a passive connection pool ready. For high-performance setups, this pool needs to be expanded to handle more simultaneous connections, otherwise you may run out of redis connections.

Tyk also lets you set a maximum number of open connections, so that you don't over-commit connections to the server.

To set you maximums and minimums, edit your tyk.conf and tyk_analytics.conf files to include:
	
	```
	"storage": {
        ...
        "optimisation_max_idle": 2000,
        "optimisation_max_active": 4000,
        ...
    },
    ```

Set the `max_idle` value to something large, we usually leave it around 2000 for HA deployments, and then set your `max_active` to your upper limit (as in, how many additional connections over the idle pool should be used)

### Analytics purging and health checks are expensive

In order to keep real-time health-check data and make it available to the Health-check API, Tyk needs to record information for every request, in a rolling window - this is an expensive operation and can limit throughput - you have two options: switch it off, or get a box with more cores.

Secondly, recording analytics is quite cheap, but purging that data is expensive, if you set your nodes up to record analytics but not purge them (set the `purge_delay` to `-1` in the front-line node), then Tyk will shovel data into Redis and leave it up to someone else to get it to Mongo.

In the above case, we recommend that a separate node is responsible solely for purging (usually installed alongside the dashboard - this can then also be the node the dashboard communicates with).

### Use the right hardware

Tyk is CPU-bound, you will get exponentially better performance the more cores you throw at Tyk. It's that simple. Tyk will automatically spread itself across all cores to handle traffic, but if expensive ops like health-checks are enabled, then those can cause keyspace contention, so again, while it helps, health-checks do throttle throughput.

### Resource limits

Make sure your system has resource limits set to handle lots of inbound traffic.

#### File handles

One thing that happens to systems that are under heavy strain is the likelihood of running out of file descriptors, so we need to make sure that this is set up properly.

Set the global file limits like this:

In the file `/etc/sysctl.conf` add:

```
fs.file-max=80000
```

For the file: `/etc/security/limits.conf`, add: 

```
*          soft     nproc          80000
*          hard     nproc          80000
*          soft     nofile         80000
*          hard     nofile         80000
root       soft     nproc          80000
root       hard     nproc          80000
root       soft     nofile         80000
root       hard     nofile         80000
```

The above is a catch-all! ON some systems and init systems the wildcard limit doesn't always work.

**Ubuntu and Upstart**

If you are using Upstart, then you'll need to set the file handle limits in the init script (`/etc/init/tyk-gateway.conf`):

```
limit nofile 50000 80000
```

#### TCP connection recycling

Use this at your own peril - it's not always recommended and you need to be sure it fits your use case, but you can squeeze a bit more performance out of a Gateway install by running this on the command line:

	sysctl -w net.ipv4.tcp_tw_recycle=1

Careful with it though, it could lead to unterminated connections.
