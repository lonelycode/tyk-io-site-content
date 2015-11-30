+++
draft = false
title = "Key changes in v1.9"
date = 2015-08-01T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_9]
    weight = -300
home = "1"
+++


In version 1.9 we have focused extensively on two things: Improved and expanded data and ease of deployment.

### Ease of deployment

Tyk is already pretty easy to deploy, being a single binary that can be dropped into a system and run right there and then without any compilation, interpreters or dependencies.

We've been speaking to our clients' DevOps teams, and one thing they particularly enjoy seeing is a secure, effective and reliable pattern for deploying third-party applications to their systems.

The other thing we've heard a lot of feedback about is the host maanger, and how having NGinX as a dependency is limiting as it's "another moving part".

What have we done to address these things?

**We got rid of the host manager** Tyk no longer needs the host manager in order to route domains to their underlying services or portals. As of v1.9 you can configure a domain for:

- The Tyk gateway itself (catch-all)
- The dashboard
- The control API (for secure access)
- On a per-API basis
- On a per-portal basis

All form within the dashboard or the configuration files.

And if you are running the full Tyk stack on a single instance, then we've made it easy for users to use Tyk the same way we used to use NginX - by having the Tyk nodes proxy the domain for the portal to the relevant organisation portal pages just like any other API.

**We've standardised our deployment packages*** As of v1.9 Tyk ships as DEB and signed RPM packages, and are provided to end-users using our GPG-signed package repository. This means that you can use APT or YUM to install Tyk and Tyk Dashboard to your servers in a repeatable and industry standard way.

We've also gone a step further and have provided init scripts for Upstart, SysV and Systemd, which means starting and stopping Tyk is as simple as `sudo service tyk-gateway start|stop|restart|status`.

We think that these two changes make it much easier for you to install, setup, manage and deploy Tyk ro any Linux distribution. We have signed repositories for Ubuntu LTS releases, Red Hat Enterprise Linux 6 and 7, and Debian Jessie.

We'll still provide the tarballs to manually install tyk on our Github Repo page, but encourage users to use our package repositories to install Tyk on supported systems.

### Improved Data

This version of tyk introduces a new feature: Uptime Awareness, with this feature, we have your tyk nodes actively poll your endpoints with specific uptime tests. Over time, Tyk collects analytics on Latency, errors and overall availability. Providing a granular view in your dashbaord to dig deeper into failures and issues.

We've made this feature as flexible as possible, enabling you to configure these tests dynamically using Service Discovery tools such as etcd or consul, while also making it possible to hook up "Host Up" and "Host Down" events to webhooks or custom javascript applications to interact with, and react to, any incidents in your infrastructure.

When enabled, Tyk can integrate this feature with it's round-robin load-balancing to remove unhealthy hosts from circulation until they come back on-line.

### Bug fixes and UX

We've overhauled the dashboard UX, making it more robust and a little faster / easier to use. The biggest change is in how we render the graphs, whih we hope you enjoy.

We've spent a lot of time fixing bugs, improving logger output and overall trying to make things more robust, performant and better.

As always, we're open to feedback on our Github repo, or in our Community forum.

### ChangeLog v1.9

- Gateway Mongo Driver updated to be compatible with MongoDB v3.0
- Fixed OAuth client listings with redis cluster
- Some latency improvements
- Key detection now checks a local in-memory cache before reaching out to Redis, keys are cached for 10 seconds, with a 5 second purge rate (so a maximum key existence of 15s). Policies will still take instant effect on keys
- key session cache is configurable, set `local_session_cache.cached_session_timeout` (default 10) and `local_session_cache.cached_session_eviction` (default 5) to the cache ttl and eviction scan times
- key session cache can be disabled: `local_session_cache.disable_cached_session_state`
- Test update to reduce number of errors, cleaner output
- Healthcheck data now stored in a sorted set, much cleaner and faster, now works with redis cluster!
- Bug fixed: Empty or invalid listen path no longer crashes proxy
- Bug fixed: Basic Auth (and Oauth BA) passwords are now hashed, this is backward compatible, plaintext passwords will still work
- OAuth access token expiry can now be set (in seconds) in the `tyk.conf` file using `oauth_token_expire:3600`
- Proxy now records accurate status codes for upstream requests for better error reporting
- Added refresh token invalidation API: `DELETE /tyk/oauth/refresh/{key}?api_id={api_id}`
- Global header injection now works, can be enabled on a per-version basis by adding `global_headers:{"header_name": "header value"}` to the version object in the API Definition, global injections also supports key metadata variables.
- Global header deletion now works: add `"global_headers_remove":["header_name", "header_name"]` to your version object
- Added request size limiter, request size limiter middleware will insist on content-length to be set, and check first against content-length value, and then actual request size value. To implement, add this to your version info:

	```
	"size_limits": [
	    {
	      "path": "widget/id",
	      "method": "PUT",
	      "size_limit": 25
	    }
	 ]
	 ```

- Request size limits can also be enforced globally, these are checked first, to implement, add `"global_size_limit": 30` to your version data.
- Adding a `key_expires_in: seconds` property to a policy definition will cause any key that is created or added using this policy to have a finite lifetime, it will expire in `now()+key_expiry` seconds, handy for free trials
- Dependency update (logrus)
- Added support for JSON Web Token (JWT), currently HMAC Signing and RSA Public/Private key signing is supported. To enable JWT on an API, add `"enable_jwt": true,` to your API Definition. Then set your tokens up with these new fields when you create them:

	```
	"jwt_data": {
		"secret": "Secret"
	}
	```

- HMAC JWT secrets can be any string, but the secret is shared. RSA secrets must be a PEM encoded PKCS1 or PKCS8 RSA private key, these can be generated on a linux box using:

	> openssl genrsa -out key.rsa 
	> openssl rsa -in key.rsa -pubout > key.rsa.pub

- Tyk JWT's MUST use the "kid" header attribute, as this is the internal access token (when creating a key) that is used to set the rate limits, policies and quotas for the user. The benefit here is that if RSA is used, then al that is stored in a Tyk installation that uses hashed keys is the hashed ID of the end user and their public key, and so very secure.
- Fixed OAuth Password flow bug where a user could generate more than one token for the same API

- Added realtime uptime monitoring, uptime monitoring means you can create a series of check requests for your upstream hosts (they do not need to be the same as the APIs being managed), and have the gateway poll them for uptime, if a host goes down (non-200 code or TCP Error) then an Event is fired (`HostDown`), when it goes back up again another event is fired (`HostUp`), this can be combined with the webhook feature for realtime alerts
- Realtime monitoring also records statistics to the database so they can be analysed or graphed later
- Real time monitoring can also be hooked into the load balancer to have the load balancer skip bad hosts for dynamic configuration
- When hosts go up and down, sentinels are activated in Redis so all nodes in a Tyk cluster can benefit
- Only one Tyk node will ever do the polling, they use a rudimentary capture-the-flag redis key to identify who is the uptime tester
- Monitoring can also be disabled if you want a non-active node to manage uptime tests and analytics purging
- The uptime test list can be refreshed live by hot-reloading Tyk
- Active monitoring can be used together with Circuit breaker to have the circuit breaker manage failing methods, while the uptime test can take a whole host offline if it becomes unresponsive
- To configure uptime tests, in your tyk.conf:

	```
	"uptime_tests": {
        "disable": false, // disable uptime tests on the node completely
        "config": {
            "enable_uptime_analytics": true,
            "failure_trigger_sample_size": 1,
            "time_wait": 5,
            "checker_pool_size": 50
        }
    }
    ```

- Check lists usually sit with API configurations, so in your API Definition:

	```
	uptime_tests: {
	    check_list: [
	      {
	        "url": "http://google.com:3000/"
	      },
	      {
	        "url": "http://posttestserver.com/post.php?dir=tyk-checker-target-test&beep=boop",
	        "method": "POST",
	        "headers": {
	          "this": "that",
	          "more": "beans"
	        },
	        "body": "VEhJUyBJUyBBIEJPRFkgT0JKRUNUIFRFWFQNCg0KTW9yZSBzdHVmZiBoZXJl"
	      }
	    ]
	  },
	  ```

- The body is base64 encoded in the second example, the first example will perform a simple GET, NOTE: using the simplified form will not enforce a timeout, while the more verbose form will fail with a 500ms timeout.
- Uptime tests can be configured from a service (e.g. etcd or consul), simply set this up in the API Definition (this is etcd):

	```
	"uptime_tests": {
	    "check_list": [],
	    "config": {
	      "recheck_wait": 12,
	      "service_discovery": {
	        "use_discovery_service": true,
	        "query_endpoint": "http://127.0.0.1:4001/v2/keys/uptimeTest",
	        "data_path": "node.value"
	      }
	    }
	},
	```
- Uptime tests by service discovery will load initially from the endpoint, it will not re-poll the service until it detects an error, at which point it will schedule a reload of the endpoint data. If used in conjunction with upstream target service discovery it enables dynamic reconfiguring (and monitoring) of services.
- The document that Tyk requires is a JSON string encoded version of the `check_list` parameter of the `uptime_tests` field, for etcd:

	```
	curl -L http://127.0.0.1:4001/v2/keys/uptimeTest -XPUT -d value='[{"url": "http://domain.com:3000/"}]'
	```

- Fixed a bug where incorrect version data would be recorded in analytics for APis that use the first URL parameter as the version (domain.com/v1/blah)
- Added domain name support (removes requirement for host manager). The main Tyk instance can have a hostname (e.g. apis.domain.com), and API Definitions can support their own domains (e.g. mycoolservice.com), multiple API definitions can have the same domain name so long as their listen_paths do not clash (so you can API 1 on mycoolservice.com/api1 and API 2 on mycoolservice.com/api2 if you set the listen_path for API 1 and API2 respectively.)
- Domains are loaded dynamically and strictly matched, so if calls for a listen path or API ID on the main tyk hostname will not work for APIs that have custom domain names set, this means services can be nicely segregated.
- If the hostname is blank, then the router is open and anything will be matched (if you are using host manager, this is the option you want as it leaves domain routing up to NginX downstream)
- Set up the main tyk instance hostname by adding `"hostname": "domain.com"` to the config
- Enable custom api-specific domains by setting `enable_custome_domains` in the tyk.conf to true
- Make an API use a custom domain by adding a `domain` element to the root object
- Custom domains will work with your SSL certs
- Refactored API loader so that it used pointers all the way down, this lessens the amount of data that needs copying in RAM (will only really affect systems running 500+ APIs)
- JSVM is now disabled by default, if you are not using JS middleware, you can reduce Tyk footprint significantly by not enabling it. To re-enable set `"enable_jsvm": true` in tyk.conf
- Fixed CORS so that if OPTIONS passthrough is enabled an upstream server can handle all pre-flight requests without any Tyk middleware intervening
- Dashboard config requires a home_dir field in order to work outside of it's home dir
- Added option to segragate control API from front-end, set `enable_api_segregation` to true and then add the hostname to `control_api_hostname`



