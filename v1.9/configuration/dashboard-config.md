+++
draft = false
title = "Dashboard configuration options"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "configuration"
+++

The Tyk Dashboard has a separate configuration file, it is small and comes packaged with the tarball. Tyk Dashboard uses
a separate configuration file as it may be installed on a different host to your Tyk nodes.

The dashboard configuration file can be found in the tyk-dashboard folder and is usually called `tyk_analytics.conf` though
it can be renamed and specified using the `--conf` flag.

The file will look like the sample below, the various fields are explained in the following sections:

    {
        "listen_port": 3000,
        "tyk_api_config": {
            "Host": "http://localhost",
            "Port": "80",
            "Secret": "352d20ee67be67f6240c4c0605b045b7"
        },
        "mongo_url": "mongodb://localhost/tyk_analytics",
        "page_size": 10,
        "admin_secret": "12345",
        "redis_port": 6379,
        "redis_host": "localhost",
        "redis_password": "",
        "redis_hosts": {
            "server1": "6379",
            "server2": "6380",
            "server23: "6381",
        },
        "enable_cluster": false
        "force_api_defaults": false,
        "notify_on_change": true,
        "license_owner": "Your Name",
        "hash_keys": true,
        "email_backend": {
            "enable_email_notifications": true,
            "code": "mandrill",
            "settings": {
                "ClientKey": "YOUR_MANDRILL_KEY"
            },
            "default_from_email": "you@domain.com",
            "default_from_name": "The Dude at Domain.com"
        },
        "hide_listen_path": false,
        "use_sentry": false,
        "sentry_code": "YOUR_SENTRY_URL",
        "sentry_js_code": "YOUR_SENTRY_URL",
        "show_org_id": true,
        "enable_duplicate_slugs": true,
        "host_config" : {
            "override_hostname": "",
            "disable_org_slug_prefix": true,
            "enable_host_names": false,
            "hostname": "",
            "portal_domains": {},
            "portal_root_path": "/portal"
        },
        "http_server_options": {
            "use_ssl": false,
            "certificates": []
        }
    }


### `listen_port`

Setting this value will change the port that tyk dashboard listens on, by default tyk will try to listen on port 3000.

### `[tyk_api_config]`

This section details a node that Tyk Dashboard can speak to, Tyk Dashboard controls Tyk using the REST API, it only requires
visibility to one node, so long as all nodes are using the same API Definitions.

If the dashboard cannot see a Tyk node, key management functions will not work properly.

#### `tyk_api_config.Host`

This is the full URL of your Tyk node.

#### `tyk_api_config.Port`

The port that Tyk is running on, defaults to `5000`

#### `tyk_api_config.Secret`

The secret that you have set in the tyk.conf file, this is the key that Tyk Dashboard will use to speak to the Tyk node's
REST API.

### `mongo_url`

The fully URL to your MongoDB instance, this can be a clustered instance if necessary and should include the database and
username / password data. **this should be the same as the credentials that your Tyk installation uses**

### `page_size`

The page size that the dashboard should use, defaults to `10` should not be edited.

### Redis

Tyk Dashboard uses Redis to store it's session data and to communicate with your Tyk nodes occasionally. It is important that
the Redis details used by the dashboard **are the same as those set for your Tyk installation**

#### `redis_port`

The port that your Redis installation is on

#### `redis_host`

The hostname for the redis collection, can be an IP address.

#### `redis_password`

If you have a set a password in your redis configuration using it's `requirepass` setting, enter it here. If this is set to
empty, Tyk Dashboard will not attempt to login to Redis.

#### `redis_database`

Set this to the index of your redis database if you are using more than one

### `enable_cluster`

Set this t true if you are using a reis cluster, then fill in the `redis_hosts` field

### `redis_hosts`

You can also specify multiple Redis hosts here, Tyk will use this array if it is not empty, or it will use the individual legacy parameters above. You can specify multiple `host:port` combinations here

### `force_api_defaults`

Forces the dashboard to use certain defaults when generating API definitions. Set this to false if you wish to manually set `listen_paths`.

### `notify_on_change`

Licensed users can use this setting to enable/disable whether Tyk Dashboard will notify all tyk nodes to hot-reload when
an API definition is changed.

### `license_owner`

Deprecated, licenses are no long required to use the dashboard

### `hash_keys`

If your Tyk gateway is using hashed tokens, set this value here to `true` so it matches, the dashboard will now operate in a mode that is
compatible with key hashing

### `[email_backend]`

Tyk supports an interface-based email back-end system, currently only Mandrill is supported out of the box. If you have a mandrill account, you can have tyk send emails on your behalf by filling in this configuration section

#### `enable_email_notifications`

Set to true to have Tyk send emails for things such as key approvals, and portal sign ups

#### `code`

The code of the back-end to use, currently only `mandrill` is supported

#### `[email_backend.settings]`

The custom settings sections for the back end

#### `settings.ClientKey`

The client key that we can use to integrate with the mandrill API

####  `default_from_email`

The address to send email from

####  `default_from_name`

The name to use when sending emails

### `hide_listen_path`

If you set this option to true, then the listen path will not be editable or visible in the dashboard

### `use_sentry`

Tyk Dashboard has sentry integration to externalise logging, set this to true to enable the logger

### `sentry_code`

If you have a sentry setup, or are using Getsentry, you can add the Sentry DSN here and Tyk will begin sending events.

### `sentry_js_code`

The Angular application theat powers the dashboard also supports sentry, to have the dashboard GUI report errors to you, add a seperate DSN here

### `show_org_id`

Determines whether the RPC ID will be shown in the Users -> Username detail page, can be useful for quickly identifying your Org ID

### `enable_duplicate_slugs`

By Default Tyk will try to stop you from using duplicate API slugs, however since Tyk v1.9 supports per-API domain names, it would be possible to have two APIs both listen to the same path (e.g. root `/`), but on different domains. 

Setting this option to true will cause the dashboard to not validate against other listen paths.

### `[host_config]`

The host config section replaces the old `hostname` option in the tyk_analytics.conf as we have more options around managing host names and domains in this version.

#### `override_hostname`

This is the equivalent of v1.8 `hostname` parameter, it will essentially stop Tyk from trying to guess which hostname to use when building URL's for the interface, set this value to whatever hostname your Tyk Gateway is running on.

#### `disable_org_slug_prefix`

Tyk wil by default try to manage domain names based on the organisation slug, so domains are like this if using the Host Manager:

    org-slug.hostname.com/api-slug

However, if you are not using the host manager, then domains are hard-coded per-api, or at a gateway level, and the org-slug moniker is not needed to construct demo URLs (e.g. for Swagger docs and the API pages). To stop this guessing behaviour, switch this option to `true` and Tyk Dashboard will stop trying to add an org-slug to the start of URL's

For legacy installs or upgrades using the host manager, leave this value as false.

#### `enable_host_names`
    
Tyk Dashboard can bind the dashboard application to a specific domain name, enable this option to have tyk dashboard only allow access on a specific domain and 404 on any other host access (not recommended)

#### `hostname`

The hostname to bind the dashboard to

#### `portal_domains`

It is possible to hard-code portal domains (these override settings set by the dashboard for routing purposes). Set `ORGID:Domainname` here so that tyk can route domain names for the portals of those organisations.

####  `portal_root_path`

The root path for the portal

### `[http_server_options]`

This section is reserved for settings relating to the HTTP server that powers the Dashboard

#### `use_ssl`

Enable to use SSL

#### `certificates`

Add a certificate block for each domain being covered by the application:

    {
        "domain_name": "*.banana.com",
        "cert_file": "new.cert.cert",
        "key_file": "new.cert.key"
    }

For more information see the [SSL section in the documentation](../ssl/)
