+++
draft = false
title = "No more licenses, Tyk Dashboard is now FREE! (also: Tyk 1.5 released)"
image = "/imgs/free-beer.jpg"
description = "With a key change in how we're approaching Tyk, and a love of free software, we've scrapped our licensing system and made these new version of Tyk and Tyk Dashboard free. Also, shiny new features like Header Injection, content transformation and a brand-spanking new API Designer."
date = 2015-02-12T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk", "Api Designer"]
+++

This is quite a big announcement, and for someone who loves open source software and for a project that seems to have garnered loads of interest from around the world, I'm incredibly
happy to announce that the licenses are now more. They are gone. Deceased. They have drawn the curtain and have joined the choir invisible. They are pushing up daisies. These, are dead licenses.

You might say that they aren't, that they are merely pining for the fjords, but it's true. We've ditched them. Tyk dashboard and all the awesome features that come with it (the hot-reload agent, the
advanced management API, the awesome new API designer), are all free to use without any limitations.

With the release of version 1.5 of Tyk and version 1.9.3 of the dashboard, we're making the software completely free to use. Tyk dashboard will remain closed-source, but this is more because we 
think it needs work before being ready to be offered up as a contributory project than because of any lingering fear of IP theft.

Existing license holders need not fret, we realised quite quickly that what our customers want is help and support. And what they can't get from the community forums, they get from us directly.
Which is why we've gone ahead and announced a whole raft of support plans for companies, agencies and enterprise clients that require help with setting up, installing, managing or generally configuring Tyk,
as well as any modifications, custom work or further consultancy. It's better for us, and more importantly, it's better for you.

Existing license holders will go straight into the basic support plan, and will remain so until their license renews at which point they can shose a service level appropriate to them.

## But wait, there's more!

That's right, we've also released a new version of Tyk and the dashboard. I'll start off with the dashboard, because it's the shiniest new feature: the API Designer.

![API Designer](/imgs/api-designer.png)

The new API designer replaces the old API manager view, restructuring it somewhat and enabling you to create API definitions and versions much more easily. It also makes the process of creating
mocks, transformations and cached pages much more intuitive while you are designing your API.

This feature is still very much in it's early stages, so any problem that you encounter, we're all ears.

### Tyk 1.5

Tyk 1.5 comes with some very shiny new features, which we think make Tyk much more competitive, two key features mainly:

#### 1. Caching

That's right, Tyk now has a caching layer that can be set on a per-endpoint basis, or across your entire stack. Cached entries live in your Redis cluster and so are blazingly fast, reducing load 
on your API and speeding up requests dramatically.

#### 2. Data Transformations

Many users have mentioned to us that they have single-auth APIs that have no real authentication mechanism, but they would like to add multiple tiered users to it without hard integration or developing
their own auth systems. Tyk 1.5 brings with it two new middleware components: Body transforms and Header injections.

Body transforms allow you to create a template of what you want your data to look like when it reaches your servers, and have Tyk modify inbound traffic on the fly, transforming one JSON structure into
the one your API recognises.

Header Injection allows you to modify and delete headers before the request reaches your server, this means that you could, for example, put your single-auth token into a request to your API
and have it hidden from the outside world, suddenly making internal APIs exposable to the outside world. So if you are looking to monetize, or simply to start opening up internal APIs, these new
features make the task easy, fast and intuitive.

#### And finally...

We've made a bunch of optimisations to the project so that it runs faster and leaner, and more tweakable settings to get more performance out of Tyk. We need to thank our community, and in particular 
*Christian Amann* who kicked of the whole optimisation cycle.

Lastly, we've made it more effective to integrate tyk with an ESB or other buffers, Tyk can now pass the entire bounced request to an event handler, so if you are using a dynamic event handler, you could
have the request proxied into a queue or processed separately without any loss of data.

Enjoy the software!

**Martin & the Tyk Team**