+++
draft = false
title = "Transforms"
date = 2014-07-29T10:57:08Z
[menu.sidebar_v1_9]
    parent = "management"
    weight = -90
+++

It is possible to modify inbound and outbound body data and header information on the fly using Tyk. This can either be done using the scriptable middleware, or, as of tyk 1.7, can be achieved using dedicated middleware.

### Modifying header information

Tyk enables you to modify header information before it leaves the proxy and is passed to your upstram API or when a response is proxied back to the client. This can be very useful in cases where you have an upstream API that
has a single authentication key, and you want to add multi-user access to it without modifying it or adding clunky authentication methods to it to support new users.

Tyk also supports the ability to use information from the key or token holder in the transform (for example, converting a Tyk-generated token into a user id that your application can use).

Here is an example scenario:

You have an API called WidgetsAPI, that takes an x-widgets-secret header to allow access, this is an internal API used by your teams but you want to expose it to your customers
and charge them for access.

You could either modify the API and add a whole user, key and access management system, or you could use Tyk to inject this header for you.

Using Tyk, you would set up your API Definition with these additions to the extended_paths.transform_headers filed:

    "extended_paths": {
        "ignored": [],
        "white_list": [],
        "black_list": [],
        "cache": ["get"],
        "transform": [],
        "transform_headers": [
            {
                "delete_headers": ["authorization"],
                "add_headers": {"x-widgets-secret": "th-secret-widget-key-is-secret"},
                "path": "widgets/{id}",
                "method": "GET"
            },
            {
                "delete_headers": ["authorization"],
                "add_headers": {"x-widgets-secret": "th-secret-widget-key-is-secret"},
                "path": "widgets",
                 "method": "GET"
            }
        ],
        "transform_response_headers": [
                {
                    "delete_headers": ["x-server-secret"],
                    "add_headers": {"x-server-id": "this-is-important"},
                    "path": "widgets/{id}",
                    "method": "GET"
                }
            ]
    }

Now Tyk keys that you create with an Access Definition rule that is set to this API and version, can have quotas, throttling and access checks applied without needing to add any new code or functionality to your existing API.

#### Using Meta Data from a key object in header transforms

It is possible to inject information that is carried within the user session object into the header space as well. Each token or key has an attached session object which contains a `meta_data` field, this is a key/value map that allows for dynamic middleware and other components to intelligently act on identity information from the inbound request without exposing it.

To use this data in your header transform simply access the special `$tyk_meta` namespace, here is a working example:

Say in your session object you have included the following metadata:

    "meta_data": {
        "uid": 12345,
        "username": "norman_bates"
    }

To use this in your header transform, your API definition path would be:

    "transform_headers": [
        {
            "delete_headers": [],
            "add_headers": {"user-id": "$tyk_meta.uid", "user-name": "$tyk_meta.username"},
            "path": "widgets/{id}",
            "method": "GET"
        },
    ]

### Injecting and Removing headers globally

In some cases you may wish to add a secure header to all outbound requests (e.g. to verify that traffic is coming from the gateway), to do so, add this to your version block:

    "version_data": {
        "versions": {
          "Default": {
            ...
            "global_headers": {
                "x-header-name": "x-header-value"
            },
            "global_headers_remove": [
                "auth_id"
            ]
            ...
          }
        }
    },

Using the `global_headers_remove` field it is possible to remove headers from all inbound requests before they are passed to your service.

### Modifying body data

Sometimes, you may also be exposing an older API, or one that uses a legacy structure for input data, or, you are actually creating a new API schema and models that are cleaner, that
you want to apply to your existing API without modifying it (it may have many legacy clients that cannot be upgraded).

As of Tyk 1.5 it is possible to modify inbound JSON requests (XML is not yet supported) using a golang template. Tyk will unmarshal the JSON data into a data structure, and then make
that data available to the template in dot-notation. Here is an example to illustrate:

Assume your inbound date structure is as follows:

    {
        "value1": "value-1",
        "value2": "value-2",
        "value_list": [
            "one",
            "two",
            "three"
        ]
    }

and you need to transform it into to a different format, you could use a golang template hat looks like this:

    {
        "value1": "{{.value2}}",
        "value2": "{{.value1}}",
        "transformed_list": [
            {{range $index, $element := .value_list}}
                {{if $index}}
                , "{{$element}}"
                {{else}}
                     "{{$element}}"
                {{end}}
            {{end}}
        ]
    }

To transform it. This template would produce the following output:

    {
        "value1": "value-1",
        "value2": "value-2",
        "transformed_list": [
            "one",
            "two",
            "three"
        ]
    }

It is not within the scope of this documentation to explain how golang templates work this is better done better elsewhere. Golang templates are powerful and versatile, and using them to transform your inbound data means any output format can be created, be it XML or a custom standard that your API understands.

It is also possible to insert keyspace meta data into a body transform (requests only), you can do this by calling the `._tyk_meta.KEYNAME` namespace, e.g.:

    {
        "value1": "value-1",
        "value2": "value-2",
        "transformed_list": [
            "one",
            "two",
            "three"
        ],
        "user-id": "._tyk_meta.uid"
    }


#### Setting up transforms in your API definition:

    "extended_paths": {
        "ignored": [],
        "white_list": [],
        "black_list": [],
        "cache": ["get"],
        "transform": [
            {
                "path": "widgets/{id}",
                "method": "POST",
                "template_data": {
                    "template_mode": "file",
                    "template_source": "./templates/transform_test.tmpl"
                }
            }
        ],
        "transform_response": [
            {
                "path": "widgets/{id}",
                "method": "POST",
                "template_data": {
                    "template_mode": "file",
                    "template_source": "./templates/transform_test.tmpl"
                }
            }
        ],
        "transform_headers": []
    }

Tyk will load and evaluate the template on start, if you modify the template, you will need to restart Tyk in order for the changes to take effect.

## A note on response modifying middleware

In order for responses to be processed by Tyk as they return via the proxy, the response middleware must be loaded for the API definition. Unlike inbound middleware, these processors are loaded only as required and must therefore be registered in advance. To do so is very simply, just add them to your API definition as follows:

    "response_processors": [
        {
          "name": "header_injector",
          "options": {
            "add_headers": {"name": "value"},
            "remove_headers": ["name"]
          }
        },
        {
          "name": "response_body_transform",
          "options": {}
        }
    ]

For the header injector response middleware, it is possible to set *global* response injection headers, this means that they do not need to be set on a version-level, any headers set up in the `options` section of the response headers object will be applied to all replies.
