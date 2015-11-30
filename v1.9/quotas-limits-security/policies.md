+++
draft = false
title = "Token Policies"
date = 2014-07-29T10:57:15Z
[menu.sidebar_v1_9]
    parent = "limits"
+++

Tyk supports setting "Key templates" for keys that are created in Tyk, in the past it was necessary to manage quotas, access rights and rate limits
manually on a key-by-key basis. With the policies feature, the basic rights for a key can be set once and then that policy can be "applied" to
any key that has the policy assigned to it.

This makes bulk management and tiered, metered, access levels of upstream APIs possible for you and your member organisations. Policy support is a
powerful new feature that goes hand in hand with the Tyk Portal, but can also be used independently with standalone Tyk implementations.

### Tyk Policy Templates

A Tyk policy template looks just like the session object that is used when you create a new access token:

	{
	  org_id: "53ac07777cbb8c2d53000002",
	  rate: 3,
	  per: 1,
	  quota_max: 1000,
	  quota_renewal_rate: 90000,
	  access_rights: {
	    b605a6f03cc14f8b74665452c263bf19: {
	      apiname: "Tyk Test API",
	      apiid: "b605a6f03cc14f8b74665452c263bf19",
	      versions: [
	        "Default"
	      ],
	      allowed_urls: []
	    },
	    "3b7e73fd18794f146aab9c2e07b787bf": {
	      apiname: "Second Test API",
	      apiid: "3b7e73fd18794f146aab9c2e07b787bf",
	      versions: [
	        "Test"
	      ],
	      allowed_urls: []
	    }
	  },
	  active: true,
	  is_inactive: false,
	  tags: [],
	  key_expires_in: 0
	}

Here you can see the various fields as they are applied to Tyk access tokens, these are all described in the access tokens section of this guide.

The important differences here are two new additions:

The `active` flag must be set to `true` for Tyk to load the policy into memory, this makes it easy to enable or disable policies without deleting them.

Secondly, the `is_inactive` flag applies to the key itself. If you set this value to `true`, any key with this policy will be disabled, you can actually
set this same value on a key object to make the single key inactive, but as part of a policy it makes it possible to deny access to a whole block of
users with a single change.

### Creating trial keys

As of v1.9 it is possible to have a policy create "Trial" keys, these are keys with a fixed expiry date set in the number of seconds from the time of the keys creations.

Although key expiry can be set in the session object on creation, when a key is created using the portal or a key request it will have a default expiry time.

To set a trial key expiry, simply add:

	`key_expires_in: 50000`

To the policy object, when the key is generated, the expiry will be forced.

### Enabling a policy in standalone / file based mode

If your Tyk configuration is standalone and configuration is being managed via the REST API without the support of the dashboard, then you will
need to set the `policies section` in your configuration file as follows:

	"policies": {
    	"policy_source": "file",
    	"policy_record_name": "./policies/policies.json"
    },

Here the `policy_source` section is set to `file` and tells Tyk to look for  policy record in the file specified in the `policy_record_name` field.
An example file is shipped with Tyk, and it will look like this:

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

The record is a single JSON object, with each named key a the policy ID, so you can list multiple policies within the single JSON object. In the above
example we have only defined a single policy called "default".

#### Enabling a policy through the dashboard

Please see the documentation in the dashboard section for details on how this is done.

### Applying a policy to a key

To apply the above policy to a key, we simply need to call the /create (or /add) endpoint in the Tyk REST API with a session object that has the
`apply_policy_id` flag set to the name `default` (or whatever you named your policy):

	{
	    "allowance": 2,
	    "rate": 3,
	    "per": 1,
	    "expires": 0,
	    "quota_max": 1000,
	    "quota_renews": 1429804261,
	    "quota_remaining": 1000,
	    "quota_renewal_rate": 90000,
	    "access_rights": {},
	    "org_id": "53ac07777cbb8c2d53000002",
	    "hmac_enabled": false,
	    "hmac_string": "",
	    "is_inactive": false,
	    "apply_policy_id": "default"
	}

Although we have set the main factors of the key, they will be overridden by the policy as soon as the key is loaded, this will happen each time
the key appears, so modifying a policy will have an instant effect on the token.
