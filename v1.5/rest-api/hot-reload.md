+++
draft = false
title = "Hot Reload"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_5]
    parent = "rest"
    
+++

### Reload API Configurations

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
    
