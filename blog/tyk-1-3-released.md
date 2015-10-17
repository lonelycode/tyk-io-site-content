+++
draft = false
title = "Tyk v1.3 and Dashboard v0.9 released, plus some quick and dirty performance graphs"
image = "/imgs/light-door.jpg"
description = "API Mocking, upstream latency reporting and node-by-node health-checks, and API Blueprint support the latest version of Tyk focuses squarely on managing your API's better and faster with ahost of convenience functions thrown in for good measure."
date = 2015-01-05T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk"]
+++

## A new version

A new year rolls in, a new version rolls out - we're very happy to announce our latest version of Tyk (1.3) and Tyk Dashboard (0.9)
which add a host of new features and one we are particularly proud of.

Part of managing a solid API lifecycle is on-boarding new developers onto development versions of API's, if you are developing a new version
of your API, but are unwilling to give anyone beta access to it, but still want your client developers to update their code, or at least have a solid
sand-box for testing their API's, you need some way of giving them access to a contract they can write to.

In order to support this there's a host of tools out there that make this possible, but we'd prefer if you could do this right 
inside your existing API gateway. With this latest release of Tyk, you can create new API versions for your API that provide mock
responses for each and every endpoint your are working with. You can even selectively allow mocks through to the upstream API 
as new functions come online.

To make this even simpler, we've added a new importer interface so we can support some of the most popular API documentation formats
and convert them into an API Version Data representation that Tyk can use. With this release, we're happy to announce support for 
Apiary's API Blueprint format. With a few keystrokes on the command line, Tyk will generate whole API mocks you can use with Tyk, or import
blueprints into existing API setups you have in Tyk as new versions. 

We feel by adding support for API Blueprint, we will be opening the door for more developers to try Tyk as well as making it really easy
to extend Tyk to support other formats such as Swagger. The end goal is squarely to make it easier for you to get up and running.

Another key change we've added (and thanks to Alex Whitman again for pointing this out as a failing in Tyk), is to be more transparent
with end-users of an API, so we've implemented a rate-limit check endpoint and response headers that ensure that developers using
your APIs always know how much of their quota is used up.

Transparency goes both ways, we've also added a new health-check API which will let API owners know:

- Upstream API latency average 
- requests per second
- Throttles per second 
- Quota violations per second
- Key failure events per second

The end goal here is to ensure that API owners and system administrators can easily check how effective Tyk is at managing traffic
as well as how end-users are experiencing upstream API latency.

This ties into the last key update of this version - Tyk now tracks upstream API latency as part of it's analytics data. This means
that you can now see how your API versions are performing from a single interface in the Tyk dashboard.

## Some quick and dirty performance tests

We've been asked by some of our users how well Tyk performs and what kind of performance curve it has, so we thought we'd throw some load at a simple node.

Now these results are by no means highly scientific, nor should they be taken as gospel. We are well aware of how flaky benchmarking and performance 
testing can be, so these are only initial, indicative results.

### The test setup 

A single Digital Ocean 2GB RAM droplet with 2 VCPU's running NGinX, Redis, MongoDB, Tyk and the Tyk Dashboard. NGinX was 
configured to just serve up a static JSON page. We chose static files because we needed a reliable baseline.

There were two tests, once directly accessing NGinX and the second test went through Tyk, which was configured to reverse proxy to the NginX server. 
Tyk was set up with a single API which used an authorization header to allow access with an unlimited quota and generous throttling limit.

The tests were ramp-up from 0 to 250 users for 5 minutes each.

### The baseline:

Baseline results gave us a rough average of just over 20ms for a page load:

![Tyk Performance Test baseline - NginX direct access](/imgs/load-base.png)

As you can see, it's pretty flat - exactly what we want, NginX is pretty nippy :-)

### With Tyk

Re-running the test with Tyk gave us a similar flatline graph, but the average response time has gone up slightly, now hovering just under 30ms:

![Tyk Performance Test with Tyk single node - NginX direct access](/imgs/load-with-tyk.png)

Now this is with a single key lookup over those 250 virtual users, just to make sure we're not missing anything we also set up a randomised 
test with 0 keys separated across virtual users. The performance profile is quite similar, with requests staying under 30ms for the most part:
 
![Tyk Performance Test with Tyk single node - NginX direct access](/imgs/load-with-tyk-multikey.png)

Take from these results what you will, we haven't taken into account parameter unpacking (if using a parameter based versioning solution, or encryption (HMAC signing) 
however we do feel that for a straight-up performance hit, Tyk runs a very low overhead. We're alway keen to get more feedback from our users, if you've been running Tyk 
in production, what kind of performance have you seen?

Happy hacking! [Download the latest version here](https://github.com/lonelycode/tyk/releases)

*Martin & the Tyk team*

## The full change log:
- Tyk now records upstream API latency as part of it's default analytics setup (Mongo DB output only)
- It is now possible to set IPs that shouldn't be tracked by analytics by setting the `ignored_ips` flag in the config file (e.g. for health checks) 
- Many core middleware configs moved into tyk common, tyk common can now be cross-seeded into other apps if necessary and is go gettable.
- Added a health-check function, calling `GET /tyk/health` with an `api_id` param, and the `X-Tyk-Authorization` header will return upstream latency average, requests per second, throttles per second, quota violations per second and key failure events per second. Can be easily extended to add more data.
- Tyk now reports quota status in response headers (Issue #27)
- Calling `/{api-id}/tyk/rate-limits` with an authorised header will return the rate limit for the current user without affecting them. Fixes issue #27
- Extended path listing (issue #16) now enabled, legacy paths will still work. You can now create an extended path set which supports forced replies (for mocking) as well as limiting by method, so `GET /widget/1234` will work and `POST /windget/1234` will not.
- You can now import API Blueprint files (JSON format) as new version definitions for your API, this includes mocking out responses. Blueprints can be added to existing API's as new versions or generate independent API definitions. 
  - Create a new definition from blueprint: `./tyk --import-blueprint=blueprint.json --create-api --org-id=<id> --upstream-target="http://widgets.com/api/"`
  - Add a version to a definition: `./tyk --import-blueprint=blueprint.json --for-api=<api_id> --as-version="2.0"`
  - Create a mock for either: use the `--as-mock` parameter.
- More test coverage throughout



