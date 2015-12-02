+++
draft = false
title = "Set up your first API"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "tutorial"
    weight = -500
+++

There are two ways to set up an API with Tyk, [file-based]({{< relref "#file-based" >}}) and [dashboard-based]({{< relref "#dashboard-based" >}})

We've described both methods in this section, starting with the dashboard based set up as this is the most often used method we've found by users.

## Dashboard Based

To set up an API in the dashboard is very easy, first off, ensure you have Tyk running and that you can log in. 

### Step 1 - Create the API Definition

We provide several defaults for the setup of an initial API, so this is very quick.

1. Browse to "System Management" -> "APIs"
2. Click "Add new API"
3. Give your API a name (this can be anything, it is for human reference only), lets call it "Test API"
4. You will notice that the listen-path and the api-slug fields will auto-populate, lets leave these defaults
5. Make sure that "Target URL" is set to "http://httpbin.org"
6. At the ottom of the panel you will see the Access method, let's leave this as Auth token so we can generate a key
7. Click the "Save" button (in the top right)

### Step 2 - Test it

Your API will have been created now and could take up to 10 seconds to propagate to the gateway nodes, lets make sure it's working:

If you are using Docker (change the IP for OSX):

	curl -H "Authorization: null" http://127.0.0.1/test-api/get

	Output:
	-------
	{
	    "error": "Key not authorised"
	}

If you are using a standard install:

	curl -H "Authorization: null" http://your-tyk-hostname:8080/test-api/get

	Output:
	-------
	{
	    "error": "Key not authorised"
	}

As we can see, tyk is blocking the request because we'v e supplie `null` as the Authorization key, lets create a key to make suer that this all works:

### Step 3 - Create a key

Creating a key is very simple, and there are many ways to do it, you can create a key via the Tyk REST API, via the Tyk Advanced API, via the dashboard GUI or via the portal developer flow.

Here we will show you two ways to create a key - [using the dashboard (simplest)]({{< relref "#creating-a-key-with-the-dashboard" >}}) and using the [Advanced API]({{< relref "#creating-a-key-with-the-advanced-api" >}}).

#### Creating a key with the Dashboard

1. Go to "System Management" -> "Keys"
2. Select "Add Key"
3. Leave all the defaults except selecting "Test API" from the "Access Rights" drop-down and clicking "Add", this will add an access rule tot he token for the API we just created. This is important, Tyk keys **must** have an access rule set
4. Click "Create", the page should scroll to the top and you will see the new key on the right-hand side in a green box

That's it, you've created a key - now we can try and use it. It's also possible to create a key using the dashboard's API, we show you in the next section. If you don't want to do this, feel free to go to step 4.

#### Creating a key with the advanced API:

To do this, you will first need your API key and the API ID of the API you want to grant access to, you can get these by:

1. Click on "System Management" -> "Users" -> Your user name [Edit]
2. The API key is under marked as "Tyk Dashboard API Access Credentials", copy this somewhere you can reference it
3. Select the "System Management" -> APIs, beside your new API Name is listed the API ID, store this as well

*(Docker users will need to change the URL on the last line to `http://tyk_dashboard.tyk_dashboard.docker:3000/api/keys` because they need to target the dashboard domain directly)*

**Create the key:**

```

curl -H "Authorization: {YOUR-API-KEY}"\
 -s\
 -H "Content-Type: application/json"\
 -X POST\
 -d '{
	"allowance": 1000,
	"rate": 1000,
	"per": 1,
	"expires": -1,
	"quota_max": -1,
	"quota_renews": 1449051461,
	"quota_remaining": -1,
	"quota_renewal_rate": 60,
	"access_rights": {
        "YOUR-API-ID": {
            "api_id": "YOUR-API-ID",
            "api_name": "Test API",
            "versions": ["Default"]
        }
    },
	"meta_data": {}
 }'\
 http://{YOUR-DASHBOARD-HOSTNAME}:3000/api/keys | python -mjson.tool

```

**What are we doing in this command?**

In this command we are instructing curl to add two headers (`-H`), one for our Authorization key, and another for the content type, we ask curl to operate silently (`-s`). We then ask curl to use the `POST` method (`-X`) and finally instruct the data to send with `-d`.

The final bit of the command instructs Tyk to use the Advanced Management API (this is actually the dashboard API), you can see this by us targeting port `3000` instead of `8080` of the gateway. 

We then pipe the output returned by curl to a json prettifier in Python so you can a clean JSON object back.

The data being sent is a Session Object, these basically outline the rights for this key (see the [Access Rights section](../../quotas-limits-security/access-control/). 

You should receive back a fully-formed session object, and the key ID for your new key:

	{
	    "api_model": {},
	    "data": {
	        "access_rights": {
	            "16daffbca6f3442e67b456dc452f9313": {
	                "allowed_urls": null,
	                "api_id": "16daffbca6f3442e67b456dc452f9313",
	                "api_name": "Test API",
	                "versions": [
	                    "Default"
	                ]
	            }
	        },
	        "allowance": 1000,
	        "apply_policy_id": "",
	        "basic_auth_data": {
	            "hash_type": "",
	            "password": ""
	        },
	        "data_expires": 0,
	        "expires": -1,
	        "hmac_enabled": false,
	        "hmac_string": "",
	        "is_inactive": false,
	        "jwt_data": {
	            "secret": ""
	        },
	        "last_check": 0,
	        "meta_data": {},
	        "monitor": {
	            "trigger_limits": null
	        },
	        "oauth_client_id": "",
	        "org_id": "53ac07777cbb8c2d53000002",
	        "per": 1,
	        "quota_max": -1,
	        "quota_remaining": -1,
	        "quota_renewal_rate": 60,
	        "quota_renews": 1449051461,
	        "rate": 1000,
	        "tags": null
	    },
	    "key_id": "53ac07777cbb8c2d53000002f9eac85230db47ba725d51d699216b77"
	}

### Step 4. Using the new Key and API

Now lets try that original curl command again to use the key against our proxied API, in the below command, we've replaced the `"Authorization": null` header to use out API key:

	curl -H "Authorization: 53ac07777cbb8c2d53000002f9eac85230db47ba725d51d699216b77" http://127.0.0.1/test-api/get

	Output
	------

	{
	  "args": {},
	  "headers": {
	    "Accept": "*/*",
	    "Accept-Encoding": "gzip",
	    "Authorization": "53ac07777cbb8c2d53000002f9eac85230db47ba725d51d699216b77",
	    "Host": "httpbin.org",
	    "User-Agent": "curl/7.35.0"
	  },
	  "origin": "127.0.0.1, 178.62.11.62",
	  "url": "http://httpbin.org/get"
	}

As you can see, the request worked, and HttpBin returned with a nice response mirroring our request.

## File Based

Tyk can be used with the dashboard or Mongo DB, this means feeding API Definitions to it using JSON documents stored on the disk. 

With Tyk, this can all be achieved using the REST API, so we'll be setting up a simple proxy to HttpBin, then generate a key and then test the API, alternatively, you can just put the JSON documents into your Tyk installations `/apps/` folder directly without using the API.

### Step 1. Create an API

This tutorial presupposes that the Tyk Gateway is running and is in file-based mode, and that you have the secret that you set in your `tyk.conf` on hand (if you don't, then open your `tyk.conf` file, it will be under `/opt/tyk-gateway/tyk.conf` and copy the field marked `secret`)

To create the API, lets send a definition to the gateway:

```

curl -H "x-tyk-authorization: 352d20ee67be67f6340b4c0605b044b7"\
 -s\
 -H "Content-Type: application/json"\
 -X POST\
 -d '{
    "name": "Test API",
    "slug": "test-api",
    "api_id": "1",
    "org_id": "53ac07777cbb8c2d53000002",
    "auth": {
        "auth_header_name": "Authorization"
    },
    "definition": {
        "location": "header",
        "key": "x-api-version"
    },
    "version_data": {
        "not_versioned": true,
        "versions": {
            "Default": {
                "name": "Default",
                "use_extended_paths": true
            }
        }
    },
    "proxy": {
        "listen_path": "/test-api/",
        "target_url": "http://httpbin.org/",
        "strip_listen_path": true
    },
    "active": true,
    "domain": ""
 }'\
 http://{YOUR-GATEWAY-HOSTNAME}:8080/tyk/apis/ | python -mjson.tool

```

If the command succeeds, you will see:

	{
	    "action": "added",
	    "key": "1",
	    "status": "ok"
	}

### Step 2. Load the API into the Gateway

In the first step all we've done is created the API and flushed it to disk in the gateway (if you check the apps directory, you should see a new API file).

To load the API, we need to run a hot reload on the gateway - this is very straightforward:

```
curl -H "x-tyk-authorization: 352d20ee67be67f6340b4c0605b044b7"\
 -s\
 -H "Content-Type: application/json"\
 http://{YOUR-GATEWAY-HOSTNAME}:8080/tyk/reload/ | python -mjson.tool
```

You should see the following output:

	{
	    "error": "",
	    "status": "ok"
	}

If you are monitoring your API Gateway output, you might see something like this:

	INFO[0912] Reload URL Structure - Scheduled
	INFO[0922] Loading API Specification from test_apps/1.json
	INFO[0922] Loading API Specification from test_apps/test_api.json
	INFO[0922] Detected 1 APIs
	INFO[0922] --> Loading API: Test API
	INFO[0922] ----> Tracking: (no host)
	INFO[0922] ----> Checking security policy: Token
	INFO[0922] Loading uptime tests...
	INFO[0922] API reload complete

### Step 4 - Test it:

To access the gateway we can do the same thing we did in the first tutorial:

	curl -H "Authorization: null" http://127.0.0.1/test-api/get

	Output:
	-------
	{
	    "error": "Key not authorised"
	}

### Step 5 - Create a key:

Now since we are using file-based mode, we will need to use the tyk Gateway REST API to generate a key to access this new API we've created.

Remember how when we created the test API, we called it `id: 1`? We'll use that as our access permission:

```
curl -H "x-tyk-authorization: 352d20ee67be67f6340b4c0605b044b7"\
 -s\
 -H "Content-Type: application/json"\
 -X POST\
 -d '{
	"allowance": 1000,
	"rate": 1000,
	"per": 1,
	"expires": -1,
	"quota_max": -1,
	"quota_renews": 1449051461,
	"quota_remaining": -1,
	"quota_renewal_rate": 60,
	"access_rights": {
        "1": {
            "api_id": "1",
            "api_name": "Test API",
            "versions": ["Default"]
        }
    },
	"meta_data": {}
 }'\
 http://{YOUR-GATEWAY-HOSTNAME}:8080/tyk/keys/create | python -mjson.tool
```

You will see a response with your new Key ID:

	{
	    "action": "create",
	    "key": "c2cb92a78f944e9a46de793fe28e847e",
	    "status": "ok"
	}

### Step 6 -  Test it:

Lets re-use that command we used in Step 4:

	curl -H "Authorization: c2cb92a78f944e9a46de793fe28e847e" http://127.0.0.1/test-api/get

	Output:
	-------
	{
	  "args": {},
	  "headers": {
	    "Accept": "*/*",
	    "Accept-Encoding": "gzip",
	    "Authorization": "c2cb92a78f944e9a46de793fe28e847e",
	    "Host": "httpbin.org",
	    "User-Agent": "curl/7.35.0"
	  },
	  "origin": "178.62.11.62, 178.62.11.62",
	  "url": "http://httpbin.org/get"
	}

And there you have it, adding an API to Tyk using it's own REST API and then adding a token to access that API. 

The API we created here is very simple and has no real settings enabled, we recommend taking a look at the extended functionality of [Tyk API Definition objects](../../api-management/api-definition-detail/) as well as a breakdown of all the various features throughout the documentation.