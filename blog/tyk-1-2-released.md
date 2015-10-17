+++
draft = false
title = "Tyk v1.2 released"
image = "/imgs/bionic-man-1.2-tyk.jpg"
description = "A new version of tyk that features a new event system, web hooks, batch processing and programatic support for new storage, identity and auth back-ends."
date = 2014-11-12T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk"]
+++

We’re very happy to announce that version 1.2 of the Tyk API gateway has been released. It’s been a while in the making, but it combines feedback from our early adopters, integrators, and contributions from the open source community.

We'd like to thank:

- [alexwhitman](https://github.com/alexwhitman)
- [rmg](https://github.com/rmg)
- [espebra](https://github.com/espebra)
- [connor4312](https://github.com/connor4312)

for their contributions, as well as everyone that has been talking to us over the community channel and getting in touch with us directly. Tyk is constantly improving and we are constantly looking for new features and updates to add to the base.

**The most significant new features in this version are:**

- IP White-listing is now supported
- Batch request processing support has been added
- A new event subsystem has been integrated
- Web hooks are now provided for system-wide events that are templatable and support multiple methods and headers

A key change in this version, which in part is why it took so long to release, is a complete refactor of how Identity (Auth) and session storage is implemented. These elements are now interfaces which can easily be extended by developers and allow different upstream APIs to have custom storage, session and authentication logic, as well as custom key generators should it be required.

The event system is extensible and easy to update with heavy use of interfaces, meaning new events are easy to add and new event handlers can be built and registered with Tyk very easily, demo code has also been added. The new web hook functionality and log handler reference implementation are both built using the Event Handler interfaces and act as proof-of-concept of this powerful new feature.

We’re very happy with how modular the code base is becoming and are eager to get more developers and integrators interested in the project, so if you have any feedback, ideas or would like to contribute, please drop us a line!

## The full change log:
- Refactored AuthManager and SessionManagers so that any identity provider / Session data handler can be used
- Added SessionExpiry to ApiDefinition, keys can expire on a date, and can be dropped form a session store set by this value, this increases security as re-auth can be forced with this
- Enables switching out storage managers per identity or session provider, so now it is fully mix and match
- API Requests require an api_id form value (either param or body) as keys are now stored (federated) on a per API basis and can live in multiple stores.
- Will not work with Dashboard 0.7
- Added IP white listing middleware, completely self-contained, very rudimentary, only explicit IP’s allowed (doesn’t handle subnets etc.), hould work with IPv4 and IPv6
- Added an Event subsystem and framework for extending and creating custom event handlers (e.g. for webhooks)
- Moved path checking to be before key checks in both Open and Closed API definitions (adopted from hotfix v1.1.1)
- Webhook event handler now exists, works like any other event handler, can be templated specifically for any output format and tailored to any method type.
- Batch request support


