+++
draft = false
title = "Plugins Overview"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_8]
    parent = "plugins"
    weight = -100
+++

### The types of plugins

There are two types of plugins that can be scripted with Tyk:

1.  **Middleware components** - these execute either `PRE` or `POST` validation. A `PRE` middleware component will execute before any session validation or token validation has taken place, while a `POST` middleware component will execute after the request has been passed through all checks and is ready to be proxied upstream

2. **Dynamic event handlers** - these components fire on certain API events (see the event handlers section), these are fired Async and do not have a cooldown timer.

All plugins have access to a limited system API (for access to resources outside of the JavaScript Virtual Machine sandbox) however the contexts of each
plugin are different and have different constraints, this API gives access to the ability to make outbound HTTP requests as well as access to the key management REST API functions.

Middleware components receive extra data (depending on their context), but all have the capability to modify the request and the session object if it
has been made available to the plugin. Usage of the Session object in a middleware component is expensive as the object needs to be retrieved, and de/re-encoded multiple times as it passes through the system.

The full API that Tyk exposes into the JSVM is documented in the ["Tyk JavaScript API" section](plugins/jsvm-api) as well as in the individual sections relating to the two types of component.
