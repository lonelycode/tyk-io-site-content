+++
draft = false
title = "Transforms"
date = 2014-07-29T10:57:08Z
[menu.sidebar_v1_5]
    parent = "management"
    weight = -90
+++

It is possible to modify outbound body data and header information on the fly using Tyk. This can either be done using the scriptable middleware, or, as of tyk 1.5, can be
achieved using dedicated middleware.

### Modifying header information

Tyk enables you to modify header information before it leaves the proxy and is passed to your upstram API. This can be very useful in cases where you have an upstream API that
has a single authentication key, and you want to add multi-user access to it without modifying it or adding clunky authentication methods to it to support new users.

Here is an example scenario:

You have an APi called WidgetsAPI, that takes an x-widgets-secret header to allow access, this is an internal API used by your teams but you want to expose it to your customers
and charge them for access.

You could either modify the APi and add a whole user, key and access management system, or you could use Tyk to inject this header for you.

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
        ]
    }
    
Now Tyk keys that you create with an Access Definition rule that is set to his API and version, can have quotas, throttling and access checks applied without needing to add
any new code or functionality to your existing API.


### Modifying body data

Sometimes, you may also be exposing an older API, or one that uses a legacy structure for input data, or, you are actually creating a new API schema and models that are cleaner, that 
you want to apply to your existing APi without modifying it (it may have many legacy clients that cannot be upgraded).

As of Tyk 1.5 it is possible to modify inbound JSON requests (XML is not yet supported) using a golang template. Tyk will unmarshal the JSOn data into a data structure, and then make
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

It is not within the scope of this documentation to explain how golang templates work this is better done better elsewhere. Golang templates are powerful and versatile, and
using them to transform your inbound data means any output format can be created, be it XML or a custom standard that your API understands.

#### Setting up transforms in your APi definition:

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
        "transform_headers": []
    }

Tyk will load and evaluate the template on start, if you modify the template, you will need to restart Tyk in order for the changes to take effect.