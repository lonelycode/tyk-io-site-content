+++
draft = false
title = "End user reporting"
date = 2014-07-29T10:57:15Z
[menu.sidebar_v1_5]
    parent = "limits"
+++

Tyk now enables your end users to get a clear picture of their current API usage without impacting their quotas.

This feature is automatically enabled, end-users can check their access rights using a simple REST call to 
`{api-id}/tyk/rate-limits/`, if you do not wish to expose this path we recommend doing so in your NGinX configuration.

The API call will only work for verified users, full details of the request:

|   **Property**    |   **Description**                  |
|   -----------     |   ---------------                  |
|   Resource URL    |   `{api-d}/tyk/rate-limits/`       |
|   Method          |   GET                              |
|   Type            |   None                             |
|   Body            |   none                             |


#### Sample request

    GET /widget-api/tyk/rate-limits/ HTTP/1.1
    Host: localhost:5000
    authorization: 658d20ee67be67f6340b4d0605b044b4
    Cache-Control: no-cache
   

#### Sample Response

    {
        "quota": {
            "quota_max": 1000,
            "quota_remaining": 768,
            "quota_renews": 1419945088
        },
        "rate_limit": {
            "requests": 1000,
            "per_unit": 60
        }
    }
    
