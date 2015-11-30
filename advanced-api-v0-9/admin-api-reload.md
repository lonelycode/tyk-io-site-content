+++
draft = false
title = "Dashboard URL Reload"
date = 2014-07-29T09:31:36Z
[menu.advanced_sidebar_v0_9]
    weight = -250
+++

Since the dashboard can have multiple URLs associated with it, these may not be loaded correctly for whatever reason. It is possible to force a URL reload by calling an API endpoint of the advanced API.

### Get a list of Keys

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/admin/system/reload`          |
|   Method          |   GET                             |
|   Type            |   None                            |
|   Body            |   None                            |
|   Param           |   None                            |

#### Sample Request:

    GET /admin/system/reload HTTP/1.1
    Host: localhost:3000
    admin-auth:12345

#### Sample Response:

	{
		"status": "ok"
	}

