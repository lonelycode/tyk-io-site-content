+++
draft = false
title = "Lists"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Tyk defines a series of 'lists' that a path can belong to, these lists are very high-level structures taht help you block access or exclude paths from specific API configurations. The list options for the Tyk dashboard can be enabled from the Endpoint Designer in the API Detail view.

### White List

Items that are checked as "white list" cause the entire API to become white-listed, this means any non-specified routes will be blocked, and only those listed in the endpoint designer will be allowed through. This is great if you wish to have very select access rules for your services.

### Black List

Adding a path to a black list will force it to be blocked. This can be useful if you are versioning your API and are deprecating a resource, instead of just making the path vanish you can block access to it.

### Ignored

Adding a path to the ignored list means that the path will not be processed for authentication data. This can be very useful if you have a non-secure endpoint (such as a health-check, or ping) that you don't need secured.

### Cache

If you specify a a path to be in the cache list, then the path will be cached by Tyk (see the advanced options section of this guide), Tyk will only ever cache safe requests, so adding a POST/PUT/DELETE request to the cache list will not work.

There are more cache options in the advanced API options panel, caching will *only* work if caching has been enabled in the API first