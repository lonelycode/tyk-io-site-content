+++
draft = false
title = "Tyk 1.7 released - one step closer"
image = "/imgs/squid.jpg"
description = "We've updated Tyk, and the portal to be bigger, better and more useful than ever. What's more, we've put our money where our mouth is. Also: Swagger support!"
date = 2015-06-23T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk", "Tyk Cloud"]
+++

We're hapy to announce the latest versions of Tyk (1.7), Tyk Dashboard (0.9.5) and our Portal software. It's been a longer release cycle than ususal, but we were a little distracted.

Over the past few months we've been busy building out Tyk 1.7 and Tyk Cloud, our cloud implementation is entirely built on the raw versions of Tyk that you can download here for free now - without any fancy customisations or liberties. Tyk Cloud is a proof of concept, highly-available, robust multi-tenant Tyk gateway implementation that makes full use of all the features available in version 1.7 and 0.9.5 of Tyk. So for those who can't wait to see those features, [sign up for an account](https://cloud.tyk.io/signup) and see what the fuss is about.

For those of you that really just want to get cracking and see what's new in version 1.7, boy do we have a treat for you!

Version 1.7 of Tyk brings many new features, of particular focus was to create a reply middleware chain, so now reply headers and reply bodies can be transformed using the same powerful templating system that was possible for outbound requests. This has been a missing feature for quite some time, and now we have a pluggable interface standard to make it easier than ever to chain out response middleware and develop new ones.

We've also extended the feature set of Open (Keyless) APIs as they seem to have become the forgotten step-child of the API gateway. Many users actually just want Tyk to act as a transparent proxy, so wev'e made that feature set a little larger by enabling non-key-based features on this API class. In this new release Open APIs now support Body Transforms, Header Injection and Caching. 

Due to (very) popular demand, we now support the Swagger file format! Swagger files can now easily be converted into Tyk-based API definitions both usng the command line and suing the API import feature in the dashboard.

A big thing for this release is notifications and events. and Tyk Dashboard and portal now have a lot more ways to speak to you when things happen: for users who want to have a lot more control over what is going on with their API users and organisations that they manage, our new monitoring plugin can notify your system via webhooks when a user or an organisation reach a percentage of their quota threshold. These triggers can be set on a per-user / per-organisation basis as well as a global limit. 

Notifications have also been extended to the dashboard and portal so that you can programatically respond to events such as API changes, key changes or key requests all via the same webhook interface.

Building on the notification and monitoring features, we have added Sentry suport for monitoring your tyk nodes and dashboard, we plan on supporting more in future, if you have a preferred system to measure errors, please let us know via the community forum.

Finally, we've made significant performance improvements to the overall application, offloading any non-returning storage calls off the main thread and minimising read/write activities where possible.

Overall this release is a series of minor improvements that add up to a more conclusive whole, with quite a few bugfixes and annoyances also taken care of - for example, the portal now supports Basic Auth signups and HMAC secret generation for underlying APIs that require it.

As always, bugs, questions, and bribes can all be directed to us via the community forum.

Cheers,
Martin