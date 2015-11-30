+++
draft = false
title = "Global Settings"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Tyk offers a few options in the API designer that can be set globally. Currently, the only global setting that has been exposed is Global Header injection, though the Tyk API definition also supports global size limits.

### Global Header Modification

Use this plugin to inject or remove headers from all requests going through the gateway. Global request manipulation occurs before path-matched modifications. This can be very useful to validate that a request has been processed by Tyk or is coming from Tyk.