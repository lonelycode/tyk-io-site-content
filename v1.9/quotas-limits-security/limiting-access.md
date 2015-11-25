+++
draft = false
title = "Securing and limiting API access"
date = 2014-07-29T10:57:15Z
[menu.sidebar_v1_9]
    parent = "limits"
+++

The `access_rights` section of the key session, if defined, will force a user into a strict access mode. This means that if there is a security entry present,
the user will **only** be granted access to the named API ID and version number, in the above case this is `APIID1` and `Default` (implying an unversioned API).

The Access Control section is the primary way to limit access to your APIs if you are running multiple Definitions. In other words, if there is nothing set in the
`access_rights` section, then the user is granted access to *all* APIs defined on the system.

The rights section is quite simple, it consists of a named set of objects which detail what API can be accessed. Tyk will use the API ID as the identifier to ensure
that some consistency and naming conventions can be used away from the human readable name. It will match the named ID in the list, and not the `api_name` section
of the object:

    "APIID1": {
        "api_name": "HMAC API",
        "api_id": "APIID1",
        "versions": [
            "Default"
        ]
    }

The sections in detail are:

- `"APIID1"` - The API ID, as set up in the definition file, this needs to match *exactly*, and will be used to find the version reference for the user on access
- `api_name` - A human friendly name to identify the API being accessed, not externalised but recommended to be set
- `versions` - This is a list of the version names (as set when you create a new API version in the definition by the key/value pair to be extracted form the header/params), e.g. if your version key is `x-api-version` and the value for one of your API versions is `31/01/2008` then the value to put into this list is `31/01/2008`.

It is recommended that for testing purposes keys should be created open to ensure access works correctly, and then for access control to be added until a full policy is defined.

A key can have access to multiple APIs and multiple versions of those APIs. Rate limits and quotas are not applied on a granular basis and will apply across all API definitions accessed.
