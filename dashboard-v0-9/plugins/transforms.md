+++
draft = false
title = "Body Transforms"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Tyk supports body transforms (both on the request, and the response). Tyk does this using powerful Golang templates. Transforms only work on JSON data types, to dynamically change a response, otherwise you could always use a transform to completely override a response.

You can use this interface to test out your templates on sample input data before committing them to Tyk. Use the "Input" field to enter the data that your service or your client will call with. Enter your template into the "Template" field, and then press "Test", Tyk will evaluate your template against the sample input and show you how it outputs.

If you select the "include session meta data" option, then Tyk will also allow you to use the meta_data from your key objects (see below for an example).

### Example body transform

Assume an inbound data structure in a request that looks like this:

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

### Meta data

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

To set metadata on a key, simply edit the key using the Key manager or add the data when the key is created for the first time. This can be done via the REST API as well.
