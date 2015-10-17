+++
draft = false
title = "Web hooks"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_6]
    parent = "event-handlers"
+++

In order to add extensibility and interoperability to Tyk, a new web hook event handler has been added, this allows a fixed
payload (which can be customised) to be delivered to any open endpoint.

The web hook handler allows the configuration of the method, body, header values, and target URL, it makes use of go templates to 
expose the event metadata.

The web hook handler also features a timeout and checksum to ensure that events that are fired multiple times do not result in the endpoint being flooded.

## Web hook cool-downs

For example, it is very likely that an `AuthFailure` event will fire on the same endpoint more than once if the requesting client is automated. However, if this
event triggered a web hook that caused an email to be sent, then if this event occure d10 times a second, the email recipient owould be flooded with emails. In an
attempt to mitigate against event such as this, you can set an `event_timeout` in seconds, in the webhook handler. This acts as a 'cool down' timer for the event 
if a message with the same request signature has been fired within the time period specified.

Tyk will create the event request object, and then MD5 checksum the raw string value of it, if this MD5 value is found in the Redis store, it will not fire, however
if it does not find it, the event will fire. The key is st once the event is fired and expires (is deleted) after the specified period in the `event_timeout` field.
 
## In the definition an configuration

The web hook event handler meta data look slike thi when added to the event handlers sections:

    {
        "handler_name":"eh_web_hook_handler",
        "handler_meta": {
            "method": "POST",
            "target_path": "http://posttestserver.com/post.php?dir=tyk-event-test",
            "template_path": "templates/default_webhook.json",
            "header_map": {"X-Tyk-Test-Header": "Tyk v1.BANANA"},
            "event_timeout": 10
    }
    
The configuration data is pretty straight-forward, it is important that all elements are set thgough an tyk will complain if it cannot initialise the handler on startup

### `handler_meta.method`

This can be any of `GET`, `PUT`, `POST`, `PATCH` or `DELETE` an will be the method used to send the request. Request types that do not support an encoded BODY will not
have the event metadata encoded as part of the request, we would advise using POST where possible.

### `handler_meta.target_path`

This is the absolute URL that should be targeted by the webhook

### `handler_meta.template_path`

By default tyk will try to use `templates/default_webhook.json` however it can be any text file, please examine the sample file for the tags to use to embed metadata into
the request body. Currently a default POSt will look like:

    {
        "event": "{{.EventType}}",
        "message": "{{.EventMetaData.Message}}",
        "path": "{{.EventMetaData.Path}}",
        "origin": "{{.EventMetaData.Origin}}",
        "key": "{{.EventMetaData.Key}}"
    }

So if your endpoint takes XML input or prefers CSV as a format, then this is equally acceptable so long as tyk can pars ethe template correctly.

### `handler_meta.header_map`

The header map will bind the set headers that are defined in this hash table to the request, if your endpoint requires some form of ID via header it can be set up 
here.

### `handler_meta.event_timeout`

This is the cool-down period for duplicate events, in seconds - set this to offset flooding of endpoints when multiple events are fired in quick succession.