+++
draft = false
title = "HMAC signatures"
date = 2014-07-29T10:55:19Z
[menu.sidebar_v1_2]
    parent = "access-control"
+++

HMAC Signing is an access token method that adds another level of security by forcing the requesting client to also send along a signature
that identifies the request temporaly to ensure that the request is from the requesting user, using a secret key that is never broadcast over the wire.

There is no ratified standard of how HMAC signatures should be implemented, there is however a [draft proposal on request signatures](https://web-payments.org/specs/ED/http-signatures/2014-02-01/#page-3) 
which is the implementation that Tyk uses. This may change over time.

An HMAC signature is essentially some additional data sent along with a request to identify the end-user using a hashed value, in our case we 
encode the 'date' header of a request, the algorithm would look like:

    base64Encode(SHA1("date:Mon, 02 Jan 2006 15:04:05 MST", secret_key))

The full request header for an HMAC request uses the standard `Authorization` header, and uses set, stripped comma-delimited fields to identify the 
user, from the draft proposal:

    Authorization: Signature keyId="hmac-key-1",algorithm="hmac-sha1",signature="Base64(HMAC-SHA1(signing string))"

Tyk **only** supports `SHA-1` encoded HMAC strings, in fact, when Tyk decodes the Authorization header, it disregards the algorithm field completely and 
assumes SHA-1 is being implemented. This may be changed over time.

The date format for an encoded string is:

    Mon, 02 Jan 2006 15:04:05 MST
    
This is the standard for most browsers, but it worth noting that if the above format is not how the date header is encoded the request will fail.

When an HMAC-signed request comes into Tyk, the key is extracted from the Authorization header, and retrieved from Redis. If the key exists
then Tyk will generate it's own signature based on the requests "date" header, if this generated signature matches the signature in the 
Authorization header the request is passed.

### Clock Skew

Tyk also implements the recommended clock-skew from the specification to prevent against replay attacks, a minimum lag of 300ms is allowed on either 
side of the date stamp, any more or less and the request will be rejected. This means that requesting machines need to be synchronised with NTP if possible.

### Additional notes

HMAC Signing is a good way to secure an API if message reliability is paramount, it goes without saying that all requests should go via TLS/SSL to ensure that 
MITM attacks can be minimised. There are many ways of managing HMAC, and because of the additional encryption processing overhead requests will be marginally slower
than more standard access methods.

## Setting up HMAC

To enable HMAC on your API, first you will need to set the API definition up to use the method, this is done in the API Definition file/object:

    {
        "name": "Tyk Test API",
        ...
        "enable_signature_checking": true,
        "use_basic_auth": false,
        "use_keyless": false,
        "use_oauth2": false,
        "auth": {
            "auth_header_name": ""
        },
        ...
    }
    
Ensure that the other methods are set to false.

When creating a user session object, the settings should be modified to reflect that an HMAC secret needs to be generated alongside the key:

    {
        ...
        "hmac_enabled": true,
        "hmac_string": "",
        ...
    }
    
Creating HMAC keys is the same as creating regular access tokens - by using the Tyk REST API. Setting the `hmac_enabled` flag to `true`, Tyk will generate a secret key for the 
key owner (which should not be modified), but will be returned by the API so you can store and report it to your end-user.