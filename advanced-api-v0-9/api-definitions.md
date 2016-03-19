+++
draft = false
title = "API Definitions"
date = 2014-07-29T09:31:36Z
[menu.advanced_sidebar_v0_9]
    weight = -260
+++

### Get List of APIs

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/apis/`                 |
|   Method          |   GET                          |
|   Type            |   None                         |
|   Body            |   None                         |
|   Param           |   None                         |

#### Sample Request

    GET /api/apis?p=0 HTTP/1.1
    Host: localhost:3000
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

#### Sample Response

    {
        "apis": [
            {
                "api_model": {},
                "api_definition": {
                    "id": "54b53e47eba6db5c70000002",
                    "name": "Nitrous Test",
                    "api_id": "39d2c98be05c424371c600bd8b3e2242",
                    "org_id": "54b53d3aeba6db5c35000002",
                    "use_keyless": false,
                    "use_oauth2": false,
                    "oauth_meta": {
                        "allowed_access_types": [],
                        "allowed_authorize_types": [
                            "token"
                        ],
                        "auth_login_redirect": ""
                    },
                    "auth": {
                        "auth_header_name": "authorization"
                    },
                    "use_basic_auth": false,
                    "notifications": {
                        "shared_secret": "",
                        "oauth_on_keychange_url": ""
                    },
                    "enable_signature_checking": false,
                    "definition": {
                        "location": "header",
                        "key": ""
                    },
                    "version_data": {
                        "not_versioned": true,
                        "versions": {
                            "Default": {
                                "name": "Default",
                                "expires": "",
                                "paths": {
                                    "ignored": [],
                                    "white_list": [],
                                    "black_list": []
                                },
                                "use_extended_paths": false,
                                "extended_paths": {
                                    "ignored": [],
                                    "white_list": [],
                                    "black_list": []
                                }
                            }
                        }
                    },
                    "proxy": {
                        "listen_path": "/39d2c98be05c424371c600bd8b3e2242/",
                        "target_url": "http://tyk.io",
                        "strip_listen_path": true
                    },
                    "custom_middleware": {
                        "pre": null,
                        "post": null
                    },
                    "session_lifetime": 0,
                    "active": true,
                    "auth_provider": {
                        "name": "",
                        "storage_engine": "",
                        "meta": null
                    },
                    "session_provider": {
                        "name": "",
                        "storage_engine": "",
                        "meta": null
                    },
                    "event_handlers": {
                        "events": {}
                    },
                    "enable_batch_request_support": false,
                    "enable_ip_whitelisting": false,
                    "allowed_ips": [],
                    "expire_analytics_after": 0
                },
                "hook_references": []
            }
            ...
        ],
        "pages": 0
    }

### Retrieve a single API by ID

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/apis/{id}`             |
|   Method          |   GET                          |
|   Type            |   None                         |
|   Body            |   None                         |
|   Param           |   None                         |

#### Sample request

    GET /api/apis/54c24242eba6db1c9a000002 HTTP/1.1
    Host: localhost
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

#### Sample Response

    {
        "api_model": {},
        "api_definition": {
            "id": "54c24242eba6db1c9a000002",
            "name": "Test",
            "api_id": "bc2f8cfb7ab241504d9f3574fe407499",
            "org_id": "54b53d3aeba6db5c35000002",
            "use_keyless": false,
            "use_oauth2": false,
            "oauth_meta": {
                "allowed_access_types": [],
                "allowed_authorize_types": [
                    "token"
                ],
                "auth_login_redirect": ""
            },
            "auth": {
                "auth_header_name": "authorization"
            },
            "use_basic_auth": false,
            "notifications": {
                "shared_secret": "",
                "oauth_on_keychange_url": ""
            },
            "enable_signature_checking": false,
            "definition": {
                "location": "header",
                "key": ""
            },
            "version_data": {
                "not_versioned": true,
                "versions": {
                    "Default": {
                        "name": "Default",
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
                                    "path": "/test-path/",
                                    "method_actions": {
                                        "GET": {
                                            "action": "no_action",
                                            "code": 200,
                                            "data": "",
                                            "headers": {}
                                        }
                                    }
                                },
                                {
                                    "path": "/test-path/reply",
                                    "method_actions": {
                                        "GET": {
                                            "action": "reply",
                                            "code": 200,
                                            "data": "{\"foo\": \"bar\"}",
                                            "headers": {
                                                "x-test": "test"
                                            }
                                        }
                                    }
                                }
                            ],
                            "white_list": [],
                            "black_list": []
                        }
                    }
                }
            },
            "proxy": {
                "listen_path": "/bc2f8cfb7ab241504d9f3574fe407499/",
                "target_url": "http://httpbin.org/",
                "strip_listen_path": true
            },
            "custom_middleware": {
                "pre": [],
                "post": []
            },
            "session_lifetime": 0,
            "active": true,
            "auth_provider": {
                "name": "",
                "storage_engine": "",
                "meta": null
            },
            "session_provider": {
                "name": "",
                "storage_engine": "",
                "meta": null
            },
            "event_handlers": {
                "events": {
                    "QuotaExceeded": [
                        {
                            "handler_name": "eh_web_hook_handler",
                            "handler_meta": {
                                "_id": "54be6c0beba6db07a6000002",
                                "event_timeout": 60,
                                "header_map": {
                                    "x-tyk-test": "123456"
                                },
                                "method": "POST",
                                "name": "Test Post",
                                "org_id": "54b53d3aeba6db5c35000002",
                                "target_path": "http://httpbin.org/post",
                                "template_path": ""
                            }
                        }
                    ]
                }
            },
            "enable_batch_request_support": true,
            "enable_ip_whitelisting": true,
            "allowed_ips": [
                "127.0.0.1"
            ],
            "expire_analytics_after": 0
        },
        "hook_references": [
            {
                "event_name": "QuotaExceeded",
                "event_timeout": 60,
                "hook": {
                    "api_model": {},
                    "id": "54be6c0beba6db07a6000002",
                    "org_id": "54b53d3aeba6db5c35000002",
                    "name": "Test Post",
                    "method": "POST",
                    "target_path": "http://httpbin.org/post",
                    "template_path": "",
                    "header_map": {
                        "x-tyk-test": "123456"
                    },
                    "event_timeout": 0
                }
            }
        ]
    }


### Create API Definition

Creating API definitions is slightly different to the core API, API definitions are wrapped inside an `api_definition` field and event handlers, such as web hooks are not embedded in th main `api_defintion` object (though they can be), webhooks are instead appended as references into the `hook_references` field, the API will embed the correct webhook data into the event handler interface.

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/apis/`                 |
|   Method          |   POST                         |
|   Type            |   None                         |
|   Body            |   Advanced API Definition      |
|   Param           |   None                         |

#### Sample Request:

    POST /api/apis HTTP/1.1
    Host: localhost:3000
    Connection: keep-alive
    Content-Type: application/json
    Content-Length: 1356
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {
        "api_definition": {
            "name": "Test",
            "auth": {
                "auth_header_name": "authorization"
            },
            "definition": {
                "location": "header",
                "key": ""
            },
            "proxy": {
                "target_url": "http://httpbin.org/"
            },
            "version_data": {
                "use_extended_paths": true,
                "not_versioned": true,
                "versions": {
                    "Default": {
                        "expires": "",
                        "name": "Default",
                        "paths": {
                            "ignored": [],
                            "white_list": [],
                            "black_list": []
                        },
                        "extended_paths": {
                            "ignored": [
                                {
                                    "path": "/test-path/",
                                    "method_actions": {
                                        "GET": {
                                            "action": "no_action",
                                            "code": 200,
                                            "data": "",
                                            "headers": {}
                                        }
                                    }
                                },
                                {
                                    "path": "/test-path/reply",
                                    "method_actions": {
                                        "GET": {
                                            "action": "reply",
                                            "code": 200,
                                            "data": "{\"foo\": \"bar\"}",
                                            "headers": {
                                                "x-test": "test"
                                            }
                                        }
                                    }
                                }
                            ],
                            "white_list": [],
                            "black_list": []
                        },
                        "use_extended_paths": true
                    }
                }
            },
            "use_oauth2": false,
            "oauth_meta": {
                "auth_login_redirect": "",
                "allowed_access_types": [],
                "allowed_authorize_types": [
                    "token"
                ]
            },
            "notifications": {
                "shared_secret": "",
                "oauth_on_keychange_url": ""
            },
            "enable_ip_whitelisting": true,
            "allowed_ips": [
                "127.0.0.1"
            ],
            "use_keyless": false,
            "enable_signature_checking": false,
            "use_basic_auth": false,
            "active": true,
            "enable_batch_request_support": true
        },
        "hook_references": [
            {
                "event_name": "QuotaExceeded",
                "hook": {
                    "api_model": {},
                    "id": "54be6c0beba6db07a6000002",
                    "org_id": "54b53d3aeba6db5c35000002",
                    "name": "Test Post",
                    "method": "POST",
                    "target_path": "http://httpbin.org/post",
                    "template_path": "",
                    "header_map": {
                        "x-tyk-test": "123456"
                    },
                    "event_timeout": 0
                },
                "event_timeout": 60
            }
        ]
    }

#### Sample Response:

    {
        "Status": "OK",
        "Message": "API created",
        "Meta": "54c24242eba6db1c9a000002"
    }


### Update API Definition

APIs that are created using the advanced management API are referenced by their internal ID instead of their API-ID.

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/apis/{internal_id}`    |
|   Method          |   PUT                          |
|   Type            |   None                         |
|   Body            |   Advanced API Definition      |
|   Param           |   None                         |

#### Sample Request:

    PUT /api/apis/54c24242eba6db1c9a000002 HTTP/1.1
    Host: localhost:3000
    Connection: keep-alive
    Content-Type: application/json
    Content-Length: 1356
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {
        "api_definition": {
            "id": "54c24242eba6db1c9a000002",
            "api_id": "bc2f8cfb7ab241504d9f3574fe407499",
            "name": "Test",
            "auth": {
                "auth_header_name": "authorization"
            },
            "definition": {
                "location": "header",
                "key": ""
            },
            "proxy": {
                "target_url": "http://httpbin.org/"
            },
            "version_data": {
                "use_extended_paths": true,
                "not_versioned": true,
                "versions": {
                    "Default": {
                        "expires": "",
                        "name": "Default",
                        "paths": {
                            "ignored": [],
                            "white_list": [],
                            "black_list": []
                        },
                        "extended_paths": {
                            "ignored": [
                                {
                                    "path": "/test-path/",
                                    "method_actions": {
                                        "GET": {
                                            "action": "no_action",
                                            "code": 200,
                                            "data": "",
                                            "headers": {}
                                        }
                                    }
                                },
                                {
                                    "path": "/test-path/reply",
                                    "method_actions": {
                                        "GET": {
                                            "action": "reply",
                                            "code": 200,
                                            "data": "{\"foo\": \"bar\"}",
                                            "headers": {
                                                "x-test": "test"
                                            }
                                        }
                                    }
                                }
                            ],
                            "white_list": [],
                            "black_list": []
                        },
                        "use_extended_paths": true
                    }
                }
            },
            "use_oauth2": false,
            "oauth_meta": {
                "auth_login_redirect": "",
                "allowed_access_types": [],
                "allowed_authorize_types": [
                    "token"
                ]
            },
            "notifications": {
                "shared_secret": "",
                "oauth_on_keychange_url": ""
            },
            "enable_ip_whitelisting": true,
            "allowed_ips": [
                "127.0.0.1"
            ],
            "use_keyless": false,
            "enable_signature_checking": false,
            "use_basic_auth": false,
            "active": true,
            "enable_batch_request_support": true
        },
        "hook_references": [
            {
                "event_name": "QuotaExceeded",
                "hook": {
                    "api_model": {},
                    "id": "54be6c0beba6db07a6000002",
                    "org_id": "54b53d3aeba6db5c35000002",
                    "name": "Test Post",
                    "method": "POST",
                    "target_path": "http://httpbin.org/post",
                    "template_path": "",
                    "header_map": {
                        "x-tyk-test": "123456"
                    },
                    "event_timeout": 0
                },
                "event_timeout": 60
            }
        ]
    }

#### Sample Response:

    {
        "Status": "OK",
        "Message": "Api updated",
        "Meta": ""
    }
