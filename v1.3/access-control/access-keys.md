+++
draft = false
title = "Standard access tokens"
date = 2014-07-29T10:55:28Z
[menu.sidebar_v1_3]
    parent = "access-control"
    weight = -110
+++


The default way Tyk handles access management to APIs is through an access token (or access key). This is a pretty common approach, and will suit
most APIs as it is easy to implement and requires little to integrate beyond associating an Access Token to a user ID in the underlying application.

These tokens can only be set in the header of a request (support for other areas of a request will soon be added). A request for a resource at the API endpoint of
`/api/widgets/12345` that uses access tokens will require the addition of a header field, (the name of the key can be defined as part of the API definition under 
the `auth` section of an API Definition file. 

An example request with a header key setting of `x-api-auth` and key value of `53ac07777cbb8c2d53000002d698728ce964432d7167596bc005c5fc` would be:

    GET /api/widgets/12345 HTTP/1.1
    Host: localhost:8080
    x-api-auth: 53ac07777cbb8c2d53000002d698728ce964432d7167596bc005c5fc
    Cache-Control: no-cache

Tyk will then attempt to find the key in Redis, if it is found and the key is active, the request is let through to the underlying API (unless
 the URL has been blacklisted). 
 
### A note on access tokens

Under the hood, security access methods are similar, and all will use some for of the authentication token format in terms of how access details are stored
and retrieved. With the exception of the OAuth2 flow. For example, if you were to use basic authentication then the username will actually be stored as a 
key with the format: `{OrganisationID}{username}` (in fact, all keys are stored this way to ensure that multiple organisations or sub-groups can utilise Tyk
keys and user names without overlap.

