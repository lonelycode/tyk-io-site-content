+++
draft = false
title = "Tyk Dashboard v0.8 released"
image = "/imgs/tyk-dashboard-v0.8.blog.png"
description = "Tyk Dashboard version 0.8 has ben released, it features bug-fixes and full compatibility with the latest version of Tyk. It's now easier than ever to manage your APIs using Tyk and Tyk Dashboard, we've made improvements to the overall UI, integration with Tyk services and better data handling."
date = 2014-11-26T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk Dashboard"]
+++

Tyk Dashboard version 0.8 has ben released, it features bug-fixes and full compatibility with the latest version of Tyk.

It's now easier than ever to manage your APIs using Tyk and Tyk Dashboard, we've made improvements to the overall UI, integration
with Tyk services and better data handling.

What's more, IP Whitelisting, Batch requests, Webhooks and API Imports are all now natively supported right from inside your
API Configuration panel.

## Key New Features

### IP Address Whitelisting 

Many of our users have Tyk installed within a closed network or in a DMZ but with specific links or access requirements for their clients
that require a secure connection. The new dashboard now makes it easy to specify which IP Addresses are allowed to access an API managed by Tyk.

### Batch Requests

Many APIs have atomic operations that can be made against resources. Sometimes, many of these requests need to be chained or made together. 
In order to alleviate the burden of making many API calls in a client, Tyk can now add batch request support to *any* api managed by Tyk, both
synchronously and asynchronously. This means, if a client requires more complex interactions with your API, they can encode them into a single
batch request. 

Batch requests run through your standard API security machinery, and are keyed to the API being accessed, ensuring that abuse of the batch feature
or attempts to spam or overload an API are thwarted at source.

### Web Hooks

Tyk v1.2 introduced an event subsystem and the ability to add webhooks to specific events in Tyk. This functionality is now available from within
the Tyk Dashboard, enabling you to create webhooks centrally, and apply them to your managed APIs for specific events. Events supported include:

- Quota violations
- Request throttling activated
- Key verification failures
- Version verification failures
- Expired key access attempt

All webhooks have cool-down periods, ensuring that the receiving endpoint of the web hook is not overloaded by multiple requests, enabling you to
throttle alerts for oft-occurring actions (e.g. request throttling or key failures).

Web hooks make it easier than ever to tie Tyk to your existing applications in a decoupled way while managing those integrations centrally.

### API Imports

Some of our users began with using Tyk in standalone mode, configuring their APIs using file-based api definitions,
they then wanted to migrate to a database-backed configuration and found they would need to re-create their definitions. 
It is now possible to quickly and easily import APIs into the dashboard, making transitioning to a centralised and managed Tyk 
installation painless.

## Get Tyk Dashboard v0.8

You can download Tyk dashboard v0.8 from our [github Releases page](https://github.com/lonelycode/tyk/releases/tag/v1.2.1) 
for [Linux AMD64](https://github.com/lonelycode/tyk/releases/download/v1.2.1/tyk-dashboard-amd64-v0.8.tar.gz), 
[ARM](https://github.com/lonelycode/tyk/releases/download/v1.2.1/tyk-dashboard-ARM-v0.8.tar.gz) and 
[i386](https://github.com/lonelycode/tyk/releases/download/v1.2.1/tyk-dashboard-i386-v0.8.tar.gz) Architectures