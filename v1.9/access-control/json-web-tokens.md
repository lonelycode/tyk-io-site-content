+++
draft = false
title = "JSON Web Tokens"
date = 2014-07-29T10:55:28Z
[menu.sidebar_v1_9]
    parent = "access-control"
    weight = -105
+++

A JSON Web Token (JWT) is a JSON-based open standard (RFC 7519) for passing claims between parties in a web application environment. The tokens are designed to be compact, URL-safe and usable especially in web browser single sign-on (SSO) context. [Find out more about JWT](http://jwt.io/introduction/)

One of the best things about a JWT is that it is cryptographically signed, and can be signed in a number of ways such as using HMAC shared keys and RSA public/private key pairs.

What is useful is when a token is issued by a third party (e.g. an OAuth provider, or an SSO interface), that third party can use a private key to sign the claims of the token, and then any third-party can verify that the claims were issued by a safe third-party by validating the signature using a publicly available public key.

### Tyk & JSON Web Tokens

Tyk supports validating an inbound token against a stored key, Tyk will not issue JWTs, but can issue a token ID that is bound to a JWT key so that inbound tokens that bear this id (key) can be validated.

Currently HMAC Signing and RSA Public/Private key signing is supported. To enable JWT on an API, add 

`"enable_jwt": true,` 

to your API Definition. Then set your tokens up with these new fields when you create them:

	```
	"jwt_data": {
		"secret": "Secret"
	}
	```

HMAC JWT secrets can be any string, but the secret is shared and therefore less secure since the same key is used for singing and validation.

RSA secrets must be a PEM encoded PKCS1 or PKCS8 RSA private key, these can be generated on a linux box using:

	openssl genrsa -out key.rsa 
	openssl rsa -in key.rsa -pubout > key.rsa.pub

When a JWT is passed to Tyk to validate, it MUST use the `kid` header field, as this is the internal access token (when creating a key) that is used to track the rate limits, policies and quotas for the token owner. 

If Tyk cannot find a `kid` header, it will try to find an ID in the `sub` field of the claims section. This is not recommended, but supported as many JWT libraries do not necessarily set the `kid` header claim (especially publicly available test generators)

The benefit here is that if RSA is used, then all that is stored in a Tyk installation that uses hashed keys is the hashed ID of the end user and their public key, and so very secure.

### JWT + Tyk Dashboard and Portal

Creating a JWT key in the dashboard simply requires the secret or key to be entered alongside the token when it is generated using the "Add Key" view.

When a JWT-enabled policy is used as part of a portal catalog entry, then Tyk will allow the end user to enter their secret as part of the sign-up process.

