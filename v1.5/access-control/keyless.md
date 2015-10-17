+++
draft = false
title = "Keyless access"
date = 2014-07-29T10:56:54Z
[menu.sidebar_v1_5]
    parent = "access-control"
+++

Tyk key-less access is basically completely open access and is not recommended. It is excellent for debugging configurations though and
will also continue to record *some* analytics data.

Keyless access will allow all requests through, all access control, versioning, quotas and rate limiting will not be possible as individual
sessions are not identified.

To implement keyless access, simply set the flag in your API Definition:

    {
        ...
        "use_keyless": true,
        "auth": {
            "auth_header_name": ""
        },
        ...
    }

This will now stop checking keys that are proxied by Tyk.