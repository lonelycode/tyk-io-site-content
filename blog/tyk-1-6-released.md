+++
draft = false
title = "Tyk Portal, Docker and a host of great changes in the new Tyk 1.6"
image = "/imgs/portal.jpg"
description = "It's that time again when we release a brand spanking new version of tyk with some awesome changes including a new portal, docker support and more powerful cluster management."
date = 2015-04-27T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk", "Docker"]
+++

For those not on the mailing list, it's hapened, we've built a [new version of Tyk](https://github.com/lonelycode/tyk/releases/tag/1.6) and released it into the wild,
and we're pretty proud of this release.

We think v1.6 changes the overall utility of Tyk, removing some odder features, and making the whole package more
rounded, better built and more stable.

What are we talking about? Well let's start with the new Tyk portal. You can now, with a few little settings, set up
a full developer on-boarding, documentation-publishing, API-enabling portal. 

### Tyk Portal

This brings the API management feature set for tyk full circle, because the portal now makes creating tiered access
levels, monetisable API categories and demo access possible through powerful Key Policies, which can be created
and applied on-the-fly to bulk-manage API tokens.

What's more, this feature is linked ot the new portal, so you can now publish an API and hook it into a Key Policy,
when developers sign up to your portal and ask for access, they will automatically be given a key that has predefined
access quotas and limits, linked ot their developer account.

This was a key feature missing from tyk, the idea of the "Developer", before there were just a list of Keys, but now
developers can be tied to API tokens, and in  turn have their own dashboard to report their metrics.

It's all retty cool, and best of all - it's completely free.

Oh, did we mention the templates are fully customisable and based on twitter bootstrap?

### API Documentation

We've had users ask about this for quite some time, and now, Tyk Portal will let you publish your API documentation
using eith Swagger or API Blueprint. Which means that when developers are perusing your API Catalogue, they will be
able to view and experiment with your API right there from the documentation, using open, portable formats!

### Hot Reload

Because tyk used to be a licensed product, we separated the autmated hot reload feature off into the Tyk host
manager, now while this was a pretty nifty feature, it did hamstring some users in that Tyk's performance profile - 
though pretty awesome - doesn't keep up with that of NginX, which meant coupling the applications together was 
difficult when managing a larger tyk install.

Well that's all gone now.

The host manager can still manage you tyk nodes, but it doesn't *have* to, the Tyk gateway node(s) will now hot-reload
as soon as a change is detected in the dashboard. What's even better, is that if you have programmatically integrated
Tyk into your systems, you can hot reload the cluster by sending an API call into a single node (e.g. behind
a load balancer) and the whole cluster will reload.

### Rate Limiter

We've made the rate limiter more robust, we've had audits that confirmed that (unfortunately), the old one, while 
it was reliable cross-process, it could be gamed in the right circumstances. So we've overhauled it and made it
use a rolling-window of data, which means it's a lot fairer now.

### Docker

We've found users have had trouble deploying tyk, and many have asked for a Docker version, well, as of version 1.5 we've
got [docker builds in dockerhub](https://hub.docker.com/u/tykio/), which makes setting up a Tyk environment much much easier, we'll go into detail
on how to set up a demo in a future blog post.

There have been many more changes in this release not mentioned here (A full [breakdown can be found in the docs](https://tyk.io/v1.6/changelog/)), 
we've just highlighted the major ones and what they mean. 

We hope you enhjoy using Tyk, tyk Dashboard and Tyk portal.

Cheers,
Martin