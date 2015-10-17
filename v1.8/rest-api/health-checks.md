+++
draft = false
title = "Health Checking"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_8]
    parent = "rest"
    
+++

### Check the health of a Tyk Node and upstream API

Tyk retains health data in Redis which reflects the current state of the API, in particular, the heatlh-check API will return:

- upstream latency average
- requests per second 
- throttles per second
- quota violations per second
- key failure events per second


|   **Property**    |   **Description**     |
|   -----------     |   ---------------     |
|   Resource URL    |   `/tyk/health`       |
|   Method          |   GET                 |
|   Type            |   None                |
|   Body            |   none                |
|   Param           |   `api_id` (required) |


#### Sample request

    GET /tyk/health/?api_id=768575647356 HTTP/1.1
    Host: localhost:5000
    X-Tyk-Authorization: 352d20ee67be67f6340b4c0605b044b7
    Cache-Control: no-cache
    
#### Sample Response

    {
        "throttle_requests_per_second": 0,
        "quota_violations_per_second": 0,
        "key_failures_per_second": 0,
        "average_upstream_latency": 0,
        "average_requests_per_second": 0
    }
    
