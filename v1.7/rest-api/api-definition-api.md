+++
draft = false
title = "API Management"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_7]
    parent = "rest"
    weight = -70
+++

API Management with the Tyk REST API is very simple, each update only afects the underlying file, and thi endpoint will only work with disk based installations, not Databse-backed ones.

API's that are added this way are flushed to to disk into the `app_path` folder using the format: `{api-id}.json`, updating existing API's that use
a different naming convention will cause those API's to be *added*, which could subsequently lead to a loading error and crash if they use the same
`listen_path`.

These methods only work on  asingle API node, if updating a cluster, it is important to ensure that all nodes are updated before initiating a reload.

### Create new API Definitions

A single Tyk node can have it's API Definitions queried, delted and updated remotely. This funcitonality enabls you to remotely update
your Tyk definitions without having to manage the files manually.

This endpoint will only update the file-based ocnfigurations of a single node, tif you are running multiple tyk instances, each will need to be
updated independently

New definitions are loaded, and then re-encoded onto disk to prevent false file writes to your apps directory, if you are running Tyk in it's default configuration, please ensure that the user that the node is running under has write permissions to the apps folder defined in your tyk.conf file.

Finally, any new definitions are not made live, they do not get loaded into the muxer, in order to load the API's, a hot-reload request should be sent to the node, this will then force a re-read of the definitions and make the definition live.


|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/apis/`        |
|   Method          |   POST                |
|   Type            |   None                |
|   Body            |   API Definition      |
|   Param           |   None                |


#### Sample request

    POST /tyk/apis/ HTTP/1.1
    Host: localhost:8080
    Connection: keep-alive
    Content-Length: 739
    Pragma: no-cache
    Cache-Control: no-cache

    {
        "name": "Quickstart test",
        "api_id": "987654321",
        "org_id": "default",
        "definition": {
            "location": "",
            "key": ""
        },
        "use_keyless": true,
        "auth": {
            "auth_header_name": ""
        },
        "version_data": {
            "not_versioned": true,
            "versions": {
                "Default": {
                    "name": "Default",
                    "expires": "3000-01-02 15:04",
                    "paths": {
                        "ignored": [],
                        "white_list": [],
                        "black_list": []
                    }
                }
            }
        },
        "proxy": {
            "listen_path": "/quickstart/",
            "target_url": "http://httpbin.org/",
            "strip_listen_path": true
        }
    }

#### Sample Response

    {
        "key": "987654321",
        "status": "ok",
        "action": "added"
    }

### Update API Definitions

Updating an API definition uses the same signature an object as a POST, however it will first ensure that the API ID that is being updated is the
same as the one in the object being `PUT`.

Updateing will completely replace the file descriptor and will not change an API Definition that has already been loaded, the hot-reload endpoint will
need to be called to push the new definition to live.


|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/apis/{api-id} |
|   Method          |   PUT                 |
|   Type            |   None                |
|   Body            |   API Definition      |
|   Param           |   None                |

#### Sample request

    PUT /tyk/apis/987654321 HTTP/1.1
    Host: localhost:8080
    Connection: keep-alive
    Content-Length: 739
    Pragma: no-cache
    Cache-Control: no-cache

    {
        "name": "Quickstart test",
        "api_id": "987654321",
        "org_id": "default",
        "definition": {
            "location": "",
            "key": ""
        },
        "use_keyless": true,
        "auth": {
            "auth_header_name": ""
        },
        "version_data": {
            "not_versioned": true,
            "versions": {
                "Default": {
                    "name": "Default",
                    "expires": "3000-01-02 15:04",
                    "paths": {
                        "ignored": [],
                        "white_list": [],
                        "black_list": []
                    }
                }
            }
        },
        "proxy": {
            "listen_path": "/quickstart/",
            "target_url": "http://httpbin.org/",
            "strip_listen_path": true
        }
    }

#### Sample Response

    {
        "key": "987654321",
        "status": "ok",
        "action": "updated"
    }

### Delete API Definitions

deleting an API definition will remove the file from the file store, th API definition will NOT be unloaded, a seperate reload request
will need to be made to disable the API endpoint.


|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/apis/{api-id} |
|   Method          |   DELETE              |
|   Type            |   None                |
|   Body            |   None                |
|   Param           |   None                |

#### Sample request

    DELETE /tyk/apis/987654321 HTTP/1.1
    Host: localhost:8080
    Connection: keep-alive
    Content-Length: 739
    Pragma: no-cache
    Cache-Control: no-cache

#### Sample Response

    {
        "key": "987654321",
        "status": "ok",
        "action": "deleted"
    }

 
