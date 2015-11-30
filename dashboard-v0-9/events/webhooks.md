+++
draft = false
title = "Webhooks"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -260
    parent="events"
+++

Webhooks are standardized web requests that Tyk can send out when certain events occur in the Tyk middleware. These hooks can be shared across APIs and updating a webhook will update the hook across all APIs that use it.

![Webhook Manager - settings](/imgs/webhook-settings.png)

Creating a webhook has four main components:

1. The name: this is a human readable name for your reference
2. The method to use: This can be either `GET`, `POST`, `PUT`, `DELETE` or `PATCH`
3. The Target: This is the URL to make the request o, your endpoint
4. A list of headers: Most webhooks will need to carry some form of security data o that the feature can't be abused, you can set any number of header values in this list.

When a webhook is fired, it will send a webhook object, these are documented in the event handler documentation in the Tyk Core section of the site.

