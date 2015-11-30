+++
draft = false
title = "Enforced Timeouts"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Enforced timeouts are a good way to ensure that your service always responds within a given amount of time. Even if a long-running process hangs. This is important in high-availability systems where response performance is crucial so errors can be dealt with cleanly.


To enable an enforced timeout on an endpoint, simply select it from the drop down and enter the timeout in seconds for the endpoint.

### Service discovery

If you are using the service discovery option, hard timeouts will force the service discovery module to refresh the host / host list.