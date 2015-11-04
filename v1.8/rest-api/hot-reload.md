+++
draft = false
title = "Hot Reload"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_8]
    parent = "rest"

+++

### Hot-reload a Tyk group

To reload a whole group of Tyk nodes (without using the dashboard or host maanger), you can send an API request to a single node, this
node will then send a notification through the pub/sub infrastructure to all other listening nodes (including the host manager if it
is being used to manage NginX) which will then trigger a global reload.

|   **Property**    |   **Description**          |
|   -----------     |   ---------------          |
|   Resource URL    |   `/tyk/reload/group`      |
|   Method          |   GET                      |
|   Type            |   None                     |
|   Body            |   none                     |


#### Sample request

    GET /tyk/reload/group HTTP/1.1
    Host: localhost:5000
    X-Tyk-Authorization: 352d20ee67be67f6340b4c0605b044b7
    Cache-Control: no-cache

#### Sample Response

    {
        "status": "ok",
        "error": ""
    }



### Hot-reload a single node

Tyk is capable of reloading configurations without having to stop serving requests, this means that API configurations
can be added at runtime, or even modified at runtime and those rules applied immediately without any downtime.

|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/reload/`      |
|   Method          |   GET                 |
|   Type            |   None                |
|   Body            |   none                |


#### Sample request

    GET /tyk/reload/ HTTP/1.1
    Host: localhost:5000
    X-Tyk-Authorization: 352d20ee67be67f6340b4c0605b044b7
    Cache-Control: no-cache

#### Sample Response

    {
        "status": "ok",
        "error": ""
    }
