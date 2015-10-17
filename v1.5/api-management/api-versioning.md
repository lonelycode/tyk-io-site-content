+++
draft = false
title = "Versioning"
date = 2014-07-29T10:57:08Z
[menu.sidebar_v1_5]
    parent = "management"
    weight = -100
+++


Versioning with Tyk has now been extended, there is a legacy mode, which will be deprecated in a future version. And an extended 
version which gives you complete control over the requests, methods, response codes, response headers and body. 

Versioning an API with Tyk is very easy and should integrate easily with how your underlying API is set up. 

### Step 1

To activate versioning in an API, simply create a version entry in the `version_data.versions` section of the API Definition:

	{
        "version-1": {
            "name": "version-1",
            "expires": "",
            "paths": {
                "ignored": [],
                "white_list": [],
                "black_list": []
            },
            "use_extended_paths": true,
            "extended_paths": {
                "ignored": [
                    {
                        "path": "/v1/ignored/noregex",
                        "method_actions": {
                            "GET": {
                                "action": "no_action",
                                "code": 200,
                                "data": "",
                                "headers": {
                                    "x-tyk-override-test": "tyk-override",
                                    "x-tyk-override-test-2": "tyk-override-2"
                                }
                            }
                        }
                    }
                ],
                "white_list": [
                    {
                        "path": "v1/allowed/whitelist/literal",
                        "method_actions": {
                            "GET": {
                                "action": "no_action",
                                "code": 200,
                                "data": "",
                                "headers": {
                                    "x-tyk-override-test": "tyk-override",
                                    "x-tyk-override-test-2": "tyk-override-2"
                                }
                            }
                        }
                    },
                    {
                        "path": "v1/allowed/whitelist/reply/{id}",
                        "method_actions": {
                            "GET": {
                                "action": "reply",
                                "code": 200,
                                "data": "flump",
                                "headers": {
                                    "x-tyk-override-test": "tyk-override",
                                    "x-tyk-override-test-2": "tyk-override-2"
                                }
                            }
                        }
                    },
                    {
                        "path": "v1/allowed/whitelist/{id}",
                        "method_actions": {
                            "GET": {
                                "action": "no_action",
                                "code": 200,
                                "data": "",
                                "headers": {
                                    "x-tyk-override-test": "tyk-override",
                                    "x-tyk-override-test-2": "tyk-override-2"
                                }
                            }
                        }
                    }
                ],
                "black_list": [
                    {
                        "path": "v1/disallowed/blacklist/literal",
                        "method_actions": {
                            "GET": {
                                "action": "no_action",
                                "code": 200,
                                "data": "",
                                "headers": {
                                    "x-tyk-override-test": "tyk-override",
                                    "x-tyk-override-test-2": "tyk-override-2"
                                }
                            }
                        }
                    }
                ]
            }
        }
    }

** Extended paths (more detail in the API Definition detail pages): **

Compared to version 1.2, this version definition is much longer. You will notice the `use_extended_paths` flag, this will
tell Tyk to load the paths from the `extended_paths` section instead of the `paths` section.

The extended path section has a similar structure to the version 1.2 method, listing out the paths and the related data in units as
part of the `ignored`, `black_list` and `white_list` lists. Each path specification has the following format:

    {
        "path": "{managed-path}",
        "method_actions": {
            "METHOD": {
                "action": "{action-code}",
                "code": {response-code},
                "data": "{body}",
                "headers": {
                    "{key}": "{value}"
                }
            }
        }
    }
    
You can set up the path to handle, and the action type, by default this should be set to `no_action` which means that Tyk will 
treat the path and method as-is without interference (i.e. if `no_action` is set, then `code`, `data`, `headers` and `body` are *not* used).

If you set `action` to `reply` Tyk will override the path and reply with settings defined in the remaining fields. This configuration can be 
used across all of the different path lists, and can be very useful when used in conjunction with the `white_list` access control list as
If it is used exclusively it can be used to mock out an entire API.
	
### Step 2

And ensure that the `definition` section o the API Definition file/object is set up to find the version data:

    "definition": {
        "location": "header",
        "key": "x-api-version"
    }

In this section you can either set the location to `header` or `url-param`, this will tell Tyk where to try and find version information for the request. When
`url-param` is set, Tyk will check query parameters or url-form-encoded parameters for the version key (so GET, POST, PUT, and DELETE methods can be used 
with data encoded in the query string or in the body of the request).

When the key is extracted from the request, the current token session is checked to see whether the user has access to the version, this is checked by attempting to 
match the version name against the key value. *this is case sensitive*.

### Step 3

Finally, ensure that the API is actually set to allow versioning, this is done by setting the `version_data.not_versioned` value to `false`.

A few notes on versioning and allowing access:

- Version expiry is applied to all keys
- Version ignored / white-listing / black listing is applied to all keys
- Version access control is only applied to keys which have access-control parameters applied to them. If a key has no `access_rights` data in the session key, then the request will be allowed through to the underlying API.

Please see the [in-depth section](/v1.3/api-management/api-definition-detail/) of this guide to read about what all of the keys in the API Definition files do.