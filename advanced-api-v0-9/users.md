+++
draft = false
title = "Users"
date = 2014-07-29T09:31:36Z
[menu.advanced_sidebar_v0_9]
    weight = -270
+++

### List users

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/api/users`                    |
|   Method          |   GET                             |
|   Type            |   None                            |
|   Body            |   None                            |
|   Param           |   None                            |

#### Sample Request:

    GET /api/users HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

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
|   Resource URL    |   `/api/users/{user-id}}`         |
|   Method          |   GET                             |
|   Type            |   None                            |
|   Body            |   None                            |
|   Param           |   None                            |

#### Sample Request:

    GET /api/users/54bd0ad9ff4329b88985aafb HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

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

### Add users

Users are created with a random password, you will need to reset the password in a second API call to set the password correctly.

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/api/users`                    |
|   Method          |   POST                            |
|   Type            |   None                            |
|   Body            |   User Object                     |
|   Param           |   None                            |

#### Sample Request:

    GET /api/users HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {
        "first_name": "Jason",
        "last_name": "Jasonson",
        "email_address": "jason@jasonsonson.com",
        "active": true
    }
    
#### Sample Response:

    {
        "Status": "OK",
        "Message": "User created",
        "Meta": ""
    }
    
### Set user password

Since users are created with a random password, you will need to reset the passwor in a second API call to set the password correctly.

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/api/users/{uid}/actions/reset`|
|   Method          |   POST                            |
|   Type            |   None                            |
|   Body            |   Password Object                 |
|   Param           |   None                            |

#### Sample Request:

    GET /api/users/54c25e845d932847067402e2/actions/reset HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {"new_password":"test123456"}
    
#### Sample Response:

    {
        "Status": "OK",
        "Message": "User password updated",
        "Meta": ""
    }

### Update user

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/api/users/{uid}`              |
|   Method          |   PUT                             |
|   Type            |   None                            |
|   Body            |   User Object                     |
|   Param           |   None                            |

#### Sample Request:

    PUT /api/users/54c25e845d932847067402e2 HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

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
      
### Delete user

|   **Property**    |   **Description**                 |
|   -----------     |   ---------------                 |
|   Resource URL    |   `/api/users/{uid}}`             |
|   Method          |   DELETE                          |
|   Type            |   None                            |
|   Body            |   None                            |
|   Param           |   None                            |

#### Sample Request:

    DELETE /api/users/54c25e845d932847067402e2 HTTP/1.1
    Host: localhost:3000
    authorization:7a7b140f-2480-4d5a-4e78-24049e3ba7f8

#### Sample Response:

    {
        "Status": "OK",
        "Message": "User deleted",
        "Meta": ""
    }
      
