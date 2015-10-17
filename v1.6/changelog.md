+++
draft = false
title = "Key changes in v1.6"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_6]
    weight = -300
home = "1"
+++

## Changes in Tyk v1.6

Version 1.6 is a step-change in Tyk, focussing on making API management more flexible. The key new feature in 1.6 is the inclusion
of a portal feature as part of the new and improved dashboard. The new Tyk portal gives you a set of flexible, bootstrap-powered
templates and a highly flexible CMS that allows you to on-board developers easily:

- Allow users to sign up to your portal with custom forms and profile fields
- Have users enroll themselves for API access with automatic or manual key approval flows
- Users can access their own analytics data to see their traffic patterns and policy data
- Policy-based access rules for APIs allow you to control blocks of keys easily
- A simlpe but powerful CMS (markdown supported) means you can create a portal that is unique to you

The portal aside, Tyk 1.6 also improves the caching facility, giving upstream control your application to decide when and for how
long a request should be cached. 

We've also taken steps in improving the security of tyk installs, keys stored in Redis can now be hashed if required and no functionality is
lost.

A much more significant change comes in how tyk handles hot reloads: tyk no longer requires the host manager to manage
auto-reloads. This change *is backwards compatible* as it can be disabled for setups that are happy using the old mthod. In 1.6 Tyk
listens to the same update channel as the host manager to handle its own reloads from the dashboard. 

This change is reflected in the Tyk REST API, it is now also possible to completely reload a Tyk cluster by sending a request to a 
single tyk node (e.g. behind a load balancer) and have it reload the group using the same mechanism as the dashboard.

This change means that the host manager, which now has improved templates for NginX, can be deployed alongside NGinX and is decoupled from
Tyk nodes, this is significant given the different performance profiles of NGinX and Tyk. this makes domain, subdomain and portal management
a seperate issue from managing your Tyk cluster. 

Finally, in a significant step towards making Tyk more robust, we've changed how our rate limiter works, the rate limiter
now uses a rolling window to count requests over a period of time, this means that straddling the end and begginning of a throttling
period to game rate limits is no longer possible. 

Overall this release focusses on making Tyk more competitive, more full-featured and a more solid all-round solution for our users.

You can download the latest version of Tyk from our downloads page or install it using Docker.

## The full changelog for 1.6:

- Added LDAP StorageHandler, enables basic key lookups from an LDAP service
- Added Policies feature, you can now define key policies for keys you generate:
    - Create a policies/policies.json file
    - Set the appropriate arguments in tyk.conf file:
		
		```
		"policies": {
			"policy_source": "file",
			"policy_record_name": "./policies/policies.json"
		}
		```

	- Create a policy, they look like this:
		
		```
		{
			"default": {
				"rate": 1000,
				"per": 1,
				"quota_max": 100,
				"quota_renewal_rate": 60,
				"access_rights": {
					"41433797848f41a558c1573d3e55a410": {
						"api_name": "My API",
						"api_id": "41433797848f41a558c1573d3e55a410",
						"versions": [
							"Default"
						]
					}
				},
				"org_id": "54de205930c55e15bd000001",
				"hmac_enabled": false
			}
		}
		```
	
	- Add a `apply_policy_id` field to your Session object when you create a key with your policy ID (in this case the ID is `default`)
	- Reload Tyk
	- Policies will be applied to Keys when they are loaded form Redis, and the updated i nRedis so they can be ueried if necessary

- Policies can invalidate whole keysets by copying over the `InActive` field, set this to true in a policy and all keys that have the policy set will be refused access.

- Added granular path white-list: It is now possible to define at the key level what access permissions a key has, this is a white-list of regex keys and apply to a whole API definition. Granular permissions are applied *after* version-based (global) ones in the api-definition. These granular permissions take the form a new field in the access rights field in either a policy definition or a session object in the new `allowed_urls` field:

	```
	{
		"default": {
			"rate": 1000,
			"per": 1,
			"quota_max": 100,
			"quota_renewal_rate": 60,
			"access_rights": {
				"41433797848f41a558c1573d3e55a410": {
					"api_name": "My API",
					"api_id": "41433797848f41a558c1573d3e55a410",
					"versions": [
						"Default"
					],
					"allowed_urls": [
						{	
							"url": "/resource/(.*),
							"methods": ["GET", "POST"] 
						}
					]
				}
			},
			"org_id": "54de205930c55e15bd000001",
			"hmac_enabled": false
		}
	}
	```

- Added `hash_keys` config option. Setting this to `true` willc ause Tyk to store all keys in Redis in a hashed representation. This will also obfuscate keys in analytics data, using the hashed representation instead. Webhooks will cotuniue to make the full API key available. This change is not backwards compatible if enabled on an existing installation.
- Added `cache_options.enable_upstream_cache_control` flag to API definitions
    - Upstream cache control is exclusive, caching must be enabled on the API, and the path to listen for upstream headers *must be defined in the `extended_paths` section*, otherwise the middleware will not activate for the path
    - Modified caching middleware to listen for two response headers: `x-tyk-cache-action-set` and `x-tyk-cache-action-set-ttl`.
    - If an upstream application replies with the header `x-tyk-cache-action-set` set to `1` (or anything non empty), and upstream control is enabled. Tyk will cache the response. 
    - If the upstream application sets `x-tyk-cache-action-set-ttl` to a numeric value, and upstream control is enabled, the cached object will be created for whatever number of seconds this value is set to.
- Added `auth.use_param` option to API Definitions, set to tru if you want Tyk to check for the API Token in the request parameters instead of the header, it will look for the value set in `auth.auth_header_name` and is *case sensitive*
- Host manager now supports Portal NginX tempalte maangement, will generate portal configuration files for NginX on load for each organisation in DB
- Host manager will now gracefully attempt reconnect if Redis goes down
- *Tyk will now reload on notifications from Redis* (dashboard signal) for cluster reloads (see below), new option in config `SuppressRedisSignalReload` will suppress this behaviour (for example, if you are still using old host manager)
- Added new group reload endpoint (for management via LB), sending a GET to /tyk/reload/group will now send a pub/sub notification via Redis which will cause all listening nodes to reload gracefully.
- Host manager can now be set to manage Tyk or not, this means host manager can be deployed alongside NGinX without managing Tyk, and Tyk nodes reloading on their own using redis pub/sub
- Rate limiter now uses a rolling window
- Tyk Dashboard API Designer completely refactored to improve reliability
