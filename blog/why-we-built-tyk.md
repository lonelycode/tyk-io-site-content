+++
draft = false
title = "How and why we built Tyk"
description = "We built Tyk because we needed a lightweight security layer for our new API and really wanted to make something with Golang. The thing is, when it comes to deciding on how to integrate key and authorisation handling it meant adding a massive additional component to our application."
image = "/imgs/building-tyk.jpg"
date = 2014-08-13T14:30:00Z
author = "Martin Buhr"
categories = ["origins", "Tyk"]
+++

Two years ago we built Loadzen, it did OK and users wanted more features, next thing you know we were extending it all over the place.

Loadzen became a monolith. A year ago we refactored the application to be completely service based, and built a really shiny API for it which our frontend was AngularJS and backend was Tornado based. It was basically a single page webapp, and we wanted to eventually expose the API to a new CLI we had built.

The thing is, when it came to deciding on how to integrate the key and authorisation handling it meant adding a massive additional component to our application, some of these were:

- Managing keys in Redis
- Machinery for revoking and re-validating keys
- Keeping bearer tokens (for the webapp) and API tokns seperate, and managing different expiry rates
- Rate limiting and quotas, we didn't want to be flooded

The list goes on, it turned out shoehorning all this functionality into our existing authentication and security infrastructure was hard, and writing rate-limiting code just seemed ridiculous for our needs. that's when we came upon the idea of using an API Gateway.

Now there's plenty out there, including paid ones - and we briefly considered using 3Scale, but what put us off all of these was that we simply wanted a component we could plug into our acrchitecture with miunimal fuss, and that would use our existing security mechanisms with a minimum of rewrites.

That's how Tyk came about, and we loved Golang, it seemed like the perfect fit - it was fast, had an excellent standard library, made it easy to write service-level components.

We dogfooded the Tyk core gateway on Loadzen for 3-4 months to make sure it wasn't leaking memory or eating up resources (we never launched the API, but we had Tyk handle all web-app based requests and bearer tokens), it performed admirably. So we decided to extend it a bit.
 
That's how we built Tyk, and why we built it. We made a conscious decision to make the core open source, nobody installs network-level components anymore if they can't look at the source. We also wanted to make sure that the project grew and grew - this is our first work with Golang, and we think that with some community support and early adopters, tyk can become a really useful tool in the system and developers arsenal.

