+++
draft = false
title = "Policy Definitions"
date = 2016-02-09T16:00:00Z
[menu.advanced_sidebar_v0_9]
    weight = -260
+++

### Get List of policies

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/portal/policies/`      |
|   Method          |   GET                          |
|   Type            |   None                         |
|   Body            |   None                         |
|   Param           |   None                         |

#### Sample Request

    GET /api/portal/policies/ HTTP/1.1
    Host: localhost:3000
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

#### Sample Response

    {
      "Data": [
        {
          "_id": "56b9fed54e86e40001000002",
          "access_rights": {
            "35447b1469df4e846894b1e87372f6d7": {
              "allowed_urls": [
                {
                  "methods": [
                    "GET",
                  ],
                  "url": "/some_resources"
                },
                {
                  "methods": [
                    "POST"
                  ],
                  "url": "/some_resource/(.*)"
                },
              ],
              "apiid": "35447b1269df4e846894b7e87312f6d7",
              "apiname": "My API",
              "versions": [
                "Default"
              ]
            }
          },
          "active": true,
          "date_created": "0001-01-01T00:00:00Z",
          "hmac_enabled": false,
          "is_inactive": false,
          "key_expires_in": 0,
          "name": "My Policy",
          "org_id": "5629ca78eebd180001000001",
          "per": 1,
          "quota_max": -1,
          "quota_renewal_rate": 60,
          "rate": 1000,
          "tags": []
        }
      ],
      "Pages": 0
    }

Notice that `apiid` field, it is different than rest of policy definitions! (See [GitHub issue 192](https://github.com/TykTechnologies/tyk/issues/192))

### Retrieve a single policy by ID

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/portal/policies/{id}`  |
|   Method          |   GET                          |
|   Type            |   None                         |
|   Body            |   None                         |
|   Param           |   None                         |

#### Sample request

    GET /api/portal/policies/56b9fed54e86e40001000002 HTTP/1.1
    Host: localhost
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

#### Sample Response

    {
      "_id": "56b9fed54e86e40001000002",
      "access_rights": {
        "35447b1469df4e846894b1e87372f6d7": {
          "allowed_urls": [
            {
              "methods": [
                "GET",
              ],
              "url": "/some_resources"
            },
            {
              "methods": [
                "POST"
              ],
              "url": "/some_resource/(.*)"
            },
          ],
          "api_id": "35447b1269df4e846894b7e87312f6d7",
          "apiname": "My API",
          "versions": [
            "Default"
          ]
        }
      },
      "active": true,
      "date_created": "0001-01-01T00:00:00Z",
      "hmac_enabled": false,
      "is_inactive": false,
      "key_expires_in": 0,
      "name": "My Policy",
      "org_id": "5629ca78eebd180001000001",
      "per": 1,
      "quota_max": -1,
      "quota_renewal_rate": 60,
      "rate": 1000,
      "tags": []
    }

### Create Policy Definition

Creating policy definitions is slightly different to the core API, API definitions are wrapped inside an `api_definition` field and event handlers, such as web hooks are not embedded in th main `api_defintion` object (though they can be), webhooks are instead appended as references into the `hook_references` field, the API will embed the correct webhook data into the event handler interface.

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/portal/policies/`      |
|   Method          |   POST                         |
|   Type            |   None                         |
|   Body            |   Advanced Policy Definition   |
|   Param           |   None                         |

#### Sample Request:

    POST /api/portal/policies/ HTTP/1.1
    Host: localhost:3000
    Connection: keep-alive
    Content-Type: application/json
    Content-Length: <body length>
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {
      "access_rights": {
        "35447b1469df4e846894b1e87372f6d7": {
          "allowed_urls": [
            {
              "methods": [
                "GET",
              ],
              "url": "/some_resources"
            },
            {
              "methods": [
                "POST"
              ],
              "url": "/some_resource/(.*)"
            },
          ],
          "api_id": "35447b1269df4e846894b7e87312f6d7",
          "apiname": "My API",
          "versions": [
            "Default"
          ]
        }
      },
      "active": true,
      "date_created": "0001-01-01T00:00:00Z",
      "hmac_enabled": false,
      "is_inactive": false,
      "key_expires_in": 0,
      "name": "My Policy",
      "org_id": "5629ca78eebd180001000001",
      "per": 1,
      "quota_max": -1,
      "quota_renewal_rate": 60,
      "rate": 1000,
      "tags": []
    }

#### Sample Response:

    {
        "Status": "OK",
        "Message": "56b9fed54e86e40001000002",
        "Meta": "null"
    }


### Update Policy Definition

|   **Property**    |   **Description**              |
|   -----------     |   ---------------              |
|   Resource URL    |   `/api/portal/policies/{id}`  |
|   Method          |   PUT                          |
|   Type            |   None                         |
|   Body            |   Advanced Policy Definition   |
|   Param           |   None                         |

#### Sample Request:

    PUT /api/portal/policies/56b9fed54e86e40001000002 HTTP/1.1
    Host: localhost:3000
    Connection: keep-alive
    Content-Type: application/json
    Content-Length: <body length>
    authorization: 7a7b140f-2480-4d5a-4e78-24049e3ba7f8

    {
      "_id": "56b9fed54e86e40001000002",
      "access_rights": {
        "35447b1469df4e846894b1e87372f6d7": {
          "allowed_urls": [
            {
              "methods": [
                "GET",
              ],
              "url": "/some_resources"
            },
            {
              "methods": [
                "POST"
              ],
              "url": "/some_resource/(.*)"
            },
          ],
          "api_id": "35447b1269df4e846894b7e87312f6d7",
          "apiname": "My API",
          "versions": [
            "Default"
          ]
        }
      },
      "active": true,
      "date_created": "0001-01-01T00:00:00Z",
      "hmac_enabled": false,
      "is_inactive": false,
      "key_expires_in": 0,
      "name": "My Policy",
      "org_id": "5629ca78eebd180001000001",
      "per": 1,
      "quota_max": -1,
      "quota_renewal_rate": 60,
      "rate": 1000,
      "tags": []
    }

#### Sample Response:

    {
        "Status": "OK",
        "Message": "Data updated",
        "Meta": ""
    }
