+++
draft = false
title = "Standard access tokens"
date = 2014-07-29T10:55:28Z
[menu.sidebar_v1_8]
    parent = "access-control"
    weight = -110
+++


The default way Tyk handles access management to APIs is through an access token (or access key). This is a pretty common approach, and will suit
most APIs as it is easy to implement and requires little to integrate beyond associating an Access Token to a user ID in the underlying application.

These tokens can be set as a *header, url parameter, or cookie name of a request*. A request for a resource at the API endpoint of
`/api/widgets/12345` that uses access tokens will require the addition of a header field, (the name of the key can be defined as part of the 
API definition under the `auth` section of an API Definition file). 

To use request parameters instead of a header, simple set the `auth.use_param` setting in thr API definition to `true`, note: unlike headers, request params are *case sensitive*).

To use a cookie name instead of a header or request parameter, set the `use_cookie` parameter to `true`. Cookie names are also case sensitive. 

An example request with a header key setting of `x-api-auth` and key value of `53ac07777cbb8c2d53000002d698728ce964432d7167596bc005c5fc` would be:

    GET /api/widgets/12345 HTTP/1.1
    Host: localhost:8080
    x-api-auth: 53ac07777cbb8c2d53000002d698728ce964432d7167596bc005c5fc
    Cache-Control: no-cache

Tyk will then attempt to find the key in Redis, if it is found and the key is active, the request is let through to the underlying API (unless
 the URL has been blacklisted). 
 
### A note on access tokens

Under the hood, security access methods are similar, and all will use some for of the authentication token format in terms of how access details are stored and retrieved. With the exception of the OAuth2 flow. 

For example, if you were to use basic authentication then the username will actually be stored as a key with the format: `{OrganisationID}{username}` (in fact, all keys are stored this way to ensure that multiple organisations or sub-groups can utilise Tyk keys and user names without overlap.

### Custom tokens

It is possible to provide tyk with your own custom tokens, this can be achieved using the tyk REST API. This is very useful if you have your own identity provider and don't want tyk to create and manage tokens for you, and instead just mirror those tokens within Tyk to off-load access control, quotas and rate limiting from your own application.

### Hashing

If you have aenabled key hashing, then the above key storage naming convention will not apply, the key will still be named in the same format, but it
will not be stored in the session store as such, instead a hashed version will be shown instead.

It is recommended to disable hashing for custom tokens

