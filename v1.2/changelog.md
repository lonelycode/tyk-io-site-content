+++
draft = false
title = "Key changes in v1.2"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_2]
    weight = -300
home = "1"
+++

Tyk version 1.2 is a stability an extensibility release, with key structural changes in how the session managers and identity providers behave. They have been 
completely refactored into interfaces and can now be extended programatically. New features such as IP white listing and an event subsystem with web hooks have also been
added alongside some bug-fixes. this version makes a breaking change to the REST API on some requests and will not work with v0.7 of the dashboard, however a 0.7.1 
compatability release has been issued to ensure ongoing compatibility.


### The full change log:
- Refactored AuthManager and SessionManagers so that any identity provider / Session data handler can be used
- Added SessionExpiry to AdpiDefinition, keys can expire on a date, and can be dropped form a session store set by this value, this increases security as re-auth can be forced with this
- Enables switching out storage managers per identity or session provider, so now it is fully mix and match
- API Requests require an `api_id` form value (either param or body) as keys are now stored (federated) on a per API basis and can live in multiple stores. THIS IS A BREAKING CHANGE
- Will not work with Dashboard 0.7
- Added IP white listing middleware, completely self-contained, very rudimentary, only explicit IP's allowed (doesn't handle subnets etc.), hould work with IPv4 and IPv6
- Added an Event subsystem and framework for extending and creating custom event handlers (e.g. for webhooks)
- Moved path checking to be before key checks in both Open and Closed API definitions (adopted from hotfix v1.1.1)
- Webhook event handler now exists, works like any other event handler, can be templated specifically for any output format and tailored to any method type.
- Batch request support

## Session handlers and Identity handlers

A key change to how tyk federates identity is that it now supports the programatic addition of session and identity providers. Developers and integrators can now 
create their own classes that implement the `AuthorisationHandler{}` and `SessionHandler{}` interfaces to add or change logic surrounding Auth, init and validation as well as 
well as their own `StorageHandler{}` interfaces if sessions or identities need to be extracted from different back-ends (e.f. MySQL databases, ActiveDirectory or 
custom session stores). If you are attempting this, we recommend getting in touch with us on Github so we can work through it together.

This change has meant that API definitions an their keys are now federated if a key has access to multiple API's, because each API, if it has a different storage engine
will need to store, update and manage that key across those engines, this means that when keys are retrieved, they are retrieved on a per-API basis. This means that certain
API requests that target key management have now changed slightly (they require an `api_id` parameter in order to pull key data, the API will inform the end user of this change
if an invalid request was made.