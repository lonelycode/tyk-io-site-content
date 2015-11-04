+++
draft = false
title = "Keyless access"
date = 2014-07-29T10:56:54Z
[menu.sidebar_v1_8]
    parent = "access-control"
+++

Tyk key-less access represents completely open access and causes Tyk to bypass any session-based middleware (middleware that requires access to token-related metadata). Most middleware will work with keyless access (header transformation, mocks, virtual paths etc.).

Open access is very useful for situations where analytics is the key reason for tracking usage, using the Tyk node as a reverse logging proxy, since it adds extremely low latency to proxied requests, it can offer a great way to monitor how an API is being used by existing users without having to use a key store.

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
