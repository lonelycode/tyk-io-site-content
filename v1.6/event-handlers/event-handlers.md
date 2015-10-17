+++
draft = false
title = "Event Handling in Tyk"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_6]
    parent = "event-handlers"
+++

Tyk version 1.2 adds the ability to configure API's with event handlers to perform specific actions when an event occurs.

There are currently two built-in event handlers: `eh_log_handler` (Log handler - mainly for debugging) and `eh_web_hook_handler` (Web Hook Handler).

The event subsystem has been designed to be easily extensible, so the community can provide additional event handlers (and add events) to the Tyk codebase or they
can be compiled into the version branch very easily for custom builds.

## Adding events to your API Definition

Event handlers are assigned by event type, multiple handlers can be fired off per event. Event Handlers are fired as go routines and so will not interfere with 
request processing. The webhook event handler aslo features a 'Flooding' check so that the same requests are not sent over and over again to an endpoint - this is
detailed in the webhook event handler documentation.

Event handlers are added to your API definition file (or database document) and have their own unique configuration parameters for each instance,
here is an example of a multi-event, multi-handler configuration:

    ...
    "event_handlers": {
			"events": {
				"QuotaExceeded": [
					{
						"handler_name":"eh_log_handler",
						"handler_meta": {
							"prefix": "LOG-HANDLER-PREFIX"
						}
					},
					{
						"handler_name":"eh_web_hook_handler",
						"handler_meta": {
							"method": "POST",
							"target_path": "http://posttestserver.com/post.php?dir=tyk-event-test",
							"template_path": "templates/default_webhook.json",
							"header_map": {"X-Tyk-Test-Header": "Tyk v1.BANANA"},
							"event_timeout": 10
						}
					}
				]
			}
    },
    ...

Each event handler that is added to the `event_handlers.events` section, is mapped by the event type (in the above case it's `QuotaExceeded`), and then a list of
each handler configuration, defined by the handler name and the handler metadata (usually some kind of configurable options for the specific handler).

So, in a less verbose example, event handlers are added by adding an event_handlers section to you API definition:

    "event_handlers": {
        "events": {
            "EVENT_NAME": [
                {
                    "handler_name": "HANDLER TO USE",
                    "handler_meta": {
                        ...
                    }
                }
            ],
            "EVENT_NAME": [
                {
                    "handler_name": "HANDLER TO USE",
                    "handler_meta": {
                        ...
                    }
                }
            ],
        }
    }

## Tyk System Events

The events currently raised by Tyk are:

- `QuotaExceeded`: Quota for a specific key has been exceeded
- `RatelimitExceeded`: Rate limit has been exceeded for a specific key
- `AuthFailure`: A key has failed authentication or has attempted access and was denied
- `KeyExpired`: A key has attempted access but is expired
- `VersionFailure`: A key has attempted access to a version it does not have permission to access

## Event metadata

Tyk events carry some additional metadata (especially important for the webhook handler). this data can be used by the webhook and exposed if 
it implements it. The metadata that comes with an event is:

- Message (string): a custom human readable message from the thing generating the event
- Path (string): The path that was accessed that led to the event being fired
- Origin (string): Origin data (if it exists)
- Key (string): The key that raised the event
- OriginatingRequest (string): Base64-encoded wire-protocol representation of the inbound request 

These metadata elements are exposed so that they can be used in templates - again, this only applies to the webhook handler in this version, however it
is a generic feature available to all handlers, for an example of how they are used, see the `templates/default_webhook.json` file, this is a golang template
that directly accesses these values and exposes them as a webhook JSON payload.

### Raw Request Data (v1.5+)

Tyk will now supply a base64 encoded representation of the original request to the event handler, if you are running a service bus or queue that stores failed, throttled or
other types of requests, you can decode this object and parse it in order to re-create the original intent of the request (e.g. for post-processing).


