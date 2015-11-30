+++
draft = false
title = "Admin API - Users"
date = 2014-07-29T09:31:36Z
[menu.advanced_sidebar_v0_9]
    weight = -270
+++

In order to make it easier to create and manage users in Tyk Dashboard, there is an Admin API version of the Users API that is not Organisation specific.

### Get a list of Users

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/admin/users/`     	     |
|   Method          |   GET                          |
|   Type            |   None                         |
|   Body            |   None                         |
|   Param           |   None                         |

#### Sample Request

    GET /admin/users/ HTTP/1.1
    Host: localhost:3000
    admin-auth: 12345

#### Sample Response:

    {
        "users": [
            {
                "api_model": {},
                "first_name": "John",
                "last_name": "Smith",
                "email_address": "john@jive.ly",
                "password": "$2a$10$mRVfrAf72N66anVNhA1KVuYaOwOrXhFzxyg6bwgZemUeVo2MNOpIa",
                "org_id": "54b53d3aeba6db5c35000002",
                "active": true,
                "id": "54b53d4bf25b920f09361526",
                "access_key": "0cf5e6c37add465a406f19807c081765"
            },
            {
                "api_model": {},
                "first_name": "Test",
                "last_name": "User",
                "email_address": "banana@test.com",
                "password": "",
                "org_id": "54b53d3aeba6db5c35000002",
                "active": true,
                "id": "54bd0ad9ff4329b88985aafb",
                "access_key": "f81ee6f0c8f2467d539c132c8a422346"
            }
        ],
        "pages": 0
    }

### Get User

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/admin/users/{user-id}}`         |
|   Method          |   GET                             |
|   Type            |   None                            |
|   Body            |   None                            |
|   Param           |   None                            |

#### Sample Request:

    GET /admin/users/54bd0ad9ff4329b88985aafb HTTP/1.1
    Host: localhost:3000
    admin-auth: 12345

#### Sample Response:

    {
        "api_model": {},
        "first_name": "Test",
        "last_name": "User",
        "email_address": "banana@test.com",
        "password": "",
        "org_id": "54b53d3aeba6db5c35000002",
        "active": true,
        "id": "54bd0ad9ff4329b88985aafb",
        "access_key": "f81ee6f0c8f2467d539c132c8a422346"
    }

### Add user

If a user is created and the password is set to empty, it will be hashed

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/admin/users`                    |
|   Method          |   POST                            |
|   Type            |   None                            |
|   Body            |   User Object                     |
|   Param           |   None                            |

#### Sample Request:

    GET /admin/users HTTP/1.1
    Host: localhost:3000
    admin-auth: 12345

    {
        "first_name": "Jason",
        "last_name": "Jasonson",
        "email_address": "jason@jasonsonson.com",
        "active": true,
        "password": "plaintext_password"
    }
    
#### Sample Response:

    {
        "Status": "OK",
        "Message": "User created",
        "Meta": ""
    }
    

### Update user

Warning - It is not possible to reset a users password by updating the user record - please use the reset password functionality for this.

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/admin/users/{uid}`              |
|   Method          |   PUT                             |
|   Type            |   None                            |
|   Body            |   User Object                     |
|   Param           |   None                            |

#### Sample Request:

    PUT /admin/users/54c25e845d932847067402e2 HTTP/1.1
    Host: localhost:3000
    admin-auth: 12345

#### Sample Response:

    {
        "api_model": {},
        "first_name": "Jason",
        "last_name": "Jasonsonson",
        "email_address": "jason@jasonsonson.com",
        "password": "$2a$10$PaQ3AgRCzanbELh9ME65xeWC7GH1VVxYskpS8lMJDirau57ZoN3Zu",
        "org_id": "54b53d3aeba6db5c35000002",
        "active": true,
        "id": "54c25e845d932847067402e2",
        "access_key": "0d8f112751c44b5d5ea28025efe86b68"
    }
