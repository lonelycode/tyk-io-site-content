+++
draft = false
title = "Key changes in v1.7"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_7]
    weight = -300
home = "1"
+++

Version 1.7 of Tyk brings many new features, of particular focus was to create a reply middleware chain, so now reply headers and reply bodies can be transformed using the same powerful templating system that was possible for outbound requests.

We've also extended the feature set of Open (Keyless) APIs as they seem to have become the forgotten step-child of the API gateway. In this new release Open API's now support Body transforms, Header Injection and Caching. A note on aching support - cache keys are on a per-IP basis, which could pose problems for clients using NAT traversal.

Due to (very) popular demand, we now support the Swagger file format, and swagger files can now easily be converted into Tyk-based API definitions.

For users who want to have a lot more control over what is going on with their API users and organisations that they manage, we have added an active monitoring plugin that can notify your system via webhooks when a user or an organisation reach a percentage of their quota threshold. These triggers can be set on a per-user / per-organisation basis as well as a global limit. Notifications have also been extended to the dashboard and portal so that you can programatically respond to events such as API changes, key changes or key requests.

Building on the notification and monitoring features, we have added Sentry suport for monitoring your tyk nodes and dashboard, we plan on supporting more in future, if you have a preferred system to measure errors, please let us know via the community forum.

Finally, we've made significant performance improvements to the overall application, offloading any non-returning storage calls of the main thread and minimising read/write activities where possible.

Overall this release is a series of minor improvements taht add up to a more conculusive whole.


## The full changelog for 1.7:

- Open APIs now support caching, body transforms and header transforms
- Added RPC storage backend for cloud-based suport. RPC server is built in vayala/gorpc, signature for the methods that need to be provideda are in the `rpc_storage_handler.go` file (see the dispatcher).
- Added `oauth_refresh_token_expire` setting in configuration, allows for customisation of refresh token expiry in OAuth flows
- Changed refresh token expiry to be 14 days by default
- Basic swagger file supoprt in command line, use `--import-swagger=petstore.json` to import a swagger definition, will create a Whitelisted API.
- Created quota monitoring for orgs and user keys, uses a webhook. To configure update `tyk.conf` to include the global check rate and target data:

```
    "monitor": {
          "enable_trigger_monitors": false,
          "configuration": {
          "method": "POST",
              "target_path": "http://posttestserver.com/post.php?dir=tyk-monitor-test",
              "template_path": "templates/monitor_template.json",
              "header_map": {"x-tyk-monitor-secret": "12345"},
              "event_timeout": 10
          },
          "global_trigger_limit": 80.0,
          "monitor_user_keys": false,
          "monitor_org_keys": true
    }
```

- It is also possible to add custom rate monitors on a per-key basis, `SessionObject` has been updated to include a "monitor" section which lets you define custom limits to trigger a quota event, add this to your key objects:

```
    "monitor": {
          "trigger_limits": [80.0, 60.0, 50.0]
      }
```

- If a custom limit is the same as a global one the event will only fire once. The output will look like this:

```
	{
	    "event": "TriggerExceeded",
	    "message": "Quota trigger reached",
	    "org": "53ac07777cbb8c2d53000002",
	    "key": "53ac07777cbb8c2d53000002c74f43ddd714489c73ea5c3fc83a6b1e",
	    "trigger_limit": "80",
	}
```

- Added response body transforms (JSON only), uses the same syntax as regular transforms, must be placed into `transform_response` list and the trasnformer must be registered under the new `response_transforms` list, otherwise it will not be activated.

```
	{
      name: "response_body_transform",
      options: {}
    }
```

- Added Response middleware chain and interface to handle response middleware. Response middleware must be declared under `response_processors` otherwise it is not loaded. Specifying options under the extended paths section will not be enough to enable response processors

```
	{
      name: "header_injector",
      options: {
      	"add_headers": {"name": "value"},
      	"remove_headers": ["name"]
  	  }
    }
```

- Added response header injection (uses the same code as the regular injector), add your path definitions to the `extended_paths.transform_response_headers` filed.
- Added `SupressDefaultOrgStore` - uses a default redis connection to handle unfound Org lookups, this is merely patching a potential hole
- Added support for Sentry DSN - monitoring can be added and enabled by adding to your `tyk.conf`:

```
	...
	"use_sentry": true,
	"sentry_code": "https://your-dsn-string",
	...
```

- Modification: Analyitcs purger (redis) now uses redis lists, much cleaner, and purge is a transaction which means multiple gateways can purge at the same time safely without risk of duplication
- Added `enforce_org_data_age` config parameter that allows for setting the expireAt in seconds for analytics data on an organisation level. (Requires the addition of a `data_expires` filed in the Session object that is larger than 0)
