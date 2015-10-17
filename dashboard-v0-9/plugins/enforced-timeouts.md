+++
draft = false
title = "Enforced Timeouts"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Enforced timeouts are a good way to ensure that your service allways responds within a given amoutn of time. Even if a long-running process hangs. This is important in high-availability systems where reposnse performance is crucial so errors can be dealt with cleanly.


![Plugins - Enforced Timeouts](/imgs/enforced-timeout.png)

To enable an enforced timeout on an endpoint, simpley select it from the drop down and enter the timeout in seconds for the endpoint.

### Service discovery

If you are using the service discovery option, hard timeouts will force the service discovery module to refresh the host / host list.