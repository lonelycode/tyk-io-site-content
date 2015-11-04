+++
draft = false
title = "Gateway configuration file"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
+++

The Tyk gateway server is configured primarily via the tyk.conf file, this file resides in `/etc/tyk` on most systems,
but can also live anywhere and be directly targeted with the `--conf` flag.

The configuration is split up into various sections, which will be detailed here.

### `listen_port`

Setting this value will change the port that tyk listens on, by default tyk will try to listen on port 5000.

### `secret`

This should be changed as soon as tyk is installed on the system. this value is used in every interaction with the Tyk
REST API, it should be passed along as the `X-Tyk-Authorization`header in any requests made. Tyk assumes that you are
sensible enough *not* to expose the management endpoints to the public and to keep this configuration value to yourself.

### `template_path`

This is the path to the tyk templates, as of the current version there is only one template `error.json` which determines the output of any errors
that are returned by Tyk to any clients attempting to connect (with the exception of the OAuth flow, which has hard-coded templates)

### `use_db_app_configs`

If you wish to store Tyk configurations in a database (only MongoDB currently supported), set this flag to `true`, on startup Tyk will attempt to connect and download
any relevant application configurations from a collection called tyk_analytics. The documents are exactly the same as the JSON configuration on disk with the exception
of a BSON ID supplied by the database

### `app_path`

If Tyk is being used in it's standard configuration, then API definitions are stored in the apps folder (by default in `/etc/tyk/apps`). this file is scanned for
files that ending in `.json` extension and interpreted at startup or reload.

### `[storage]`

This section of the configuration is to hold the Rdis configuration.

#### `storage.type`

This should be set to 'redis' (lowercase), the previous backup and testing option of `csv` is deprecated.

#### `storage.host`

The redis host, by default this is set to localhost, but for production this should be set to a cluster

#### `storage.port`

The redis instance port

#### `storage.username`

Not used, may be used in future for other back-ends

#### `storage.password`

if your redis instance has a password set for access, you can tell Tyk about it here

#### `storage.optimisation_max_idle`

Set the number of maximum idle connections in the Redis connection pool, defaults to 100, set to higher if expecting more traffic.

### `enable_analytics`

Tyk is capable of recording every hit to your API into a database with various filtering parameters, set this value
to `true` an fill in the sub-section below to enable logging.

Note that Tyk will store traffic data to Redis initially (for performance reasons) and then purge the data from redis
into MongoDB/CSV on a regular basis as determined by the `purge_delay`. This should prevent your Redis instance from
getting too bloated.

### `[analytics_config]`

This section defines how to store analytics data

#### `analytics_config.type`

The type of storage engine to use, currently `mongo` and `csv` are supported, **note**: MongoDB stores much richer data.

#### `analytics_config.csv_dir`

The folder to purge data into if the `csv` type is set.

#### `analytics_config.mongo_url`

The mongo address to your instance, should be of the form: `mongodb://user:pass@host1.your-mongo.com:PORT,optional_host2.your-mongo.com:PORT/DB_NAME`.

#### `analytics_config.mongo_db_name`

Not used

#### `analytics_config.mongo_collection`

Collection name to store the analytics data in

#### `analytics_config.purge_delay`

The delay between purging traffic data from your redis instance. Can be set as frequently or as delayed as needed, this
runs as a separate routine to the main server and so should not affect performance. We recommend setting this to a low value
for busy APIs. This also determines how up-to-date the data is in your data store.

#### `analytics_config.ignored_ips`

Adding IP addresses to this list will cause Tyk to ignore these IP's in the analytics data, these IP addresses
will not produce an analytics log record. This is useful for health checks and other samplers that might skew usage data.

### `[health_checks]`

This section enables the configuration of the health-check API endpoint and the size of the sample data cache (in seconds).

#### `health_check.enable_health_checks`

Setting this value to `true` will enable the health-check endpoint on `/tyk/health`.

#### `health_check.health_check_value_timeouts`

This setting defaults to `60`, this is the time window that Tyk will use to sample health-check data. Increase this value for
more accurate data (larger sample period), and decrease for less accurate. The reason this value is configurable is because sample
data takes up space in your Redis DB to store the data to calculate samples, on high-availability systems this may not be desirable
and smaller values may be preferred.

### `optimisations_use_async_session_write`

set this value to true to have Tyk manage session data using a goroutine, this is quite safe an can significantly boost performance in HA environments where Tyk is installed
on a machine with multiple cores.

### `allow_master_keys`

If this value is set to `true`, session objects (key definitions) that do not have explicit access rights set will be allowed by Tyk. This
means that keys that are created have access to ALL APIs, which in many cases is unwanted behaviour unless you are sure about what you are doing.

### `policies`

The policies section allows you to define where Tyk can find it's policy templates. Policy templates are similar to key definitions in that
they allow you to set quotas, access rights and rate limits for keys.

Policies are loaded when Tyk starts and if changed require a hot-reload so they are loaded into memory.

A policy can be defined in a file (stand alone mode) or from the same database as the dashboard.

#### `policies.policy_source`

Set this value to `file` to look on the filesystem for a definition file, set to `mongo` to use the dashboard.

#### `policies.policy_record_name`

The record name to use, this can either be a fully-qualfied path or a collection name in MongoDB.

### `hash_keys`

Set this value to `true` to enable key hashing, this will start hashing all keys that are generated by Tyk in Redis. enabling this
means that keys that are created are only exposed once, during creation. If the key is lost or misplaced after this it will
not be retrievable (however it's hash can still be deleted, if known).

If this is set to True, the same alue should be enabled in the dashboard configuration so that the UI can react appropriately.

### `suppress_redis_signal_reload`

Tyk v1.6 will auto-reload when a change is detected when using the dashboard, this used to be done by the host-manager but is now directly
integrated into the gateway. For backwards compatability, this behaviour can be supressed by setting this value to `true`.

### `use_sentry`

Set this to true to enable the sentry logger, you must specify a sentry DSN iunder `sentry_code`

### `sentry_code`

The Sentry-assigned DSN (a kind of URL endpoint) that Tyk can send log data to

### `enforce_org_data_age`

Set to true to have tyk enforce data age on analytics data sent to the Mongo DB purger, must be accompanied by an additional paramter to an organisation session object called `data_expires` which is larger than 0, this will define in seconds when analytics records for the inbound request will expire.

### `enforce_org_quotas`

Set this value to `true` to have the Tyk node enforce Organisation quotas on the APIs it is managing.

### `experimental_process_org_off_thread`

Setting this option to `true` will cause the organisation quota checker to handle quota limits off the main request pipeline, this can provide a speed boost in some situations. enabling this will cause quotas to be enforced by a "lock", in order to re-access the API once the quota has reset, the lock must be broken by sending one failed request, this will cause the sentinel to be disabled and allow traffic to pass normally again.

### `http_server_options`

Set these options to hard-code values into the way the HTTP server behaves. **this is highly experimental and should only b used for extreme tuning pruposes** it is not recommended to be used unless absolutely necessary.

	```
	"http_server_options": {
        "override_defaults": true,
        "read_timeout": 10,
        "write_timeout": 10
    }
    ```

For more resilient connection management, it is suggested to use the `close_connections` option below.

### `close_connections`

Set this value to `true` to force Tyk to get clients to close the connection with the client, otherwise the connections will remain open for as long as your OS keeps TCP connections open, this can cause a file-handler limit to be exceeded.

### `disable_virtual_path_blobs`

If you do not wish for virtual path JavaScript code that is loaded from the dashboard to run on virtual endpoints in the node, set this value to false and the code will not be loaded into the VM when the API definition initialises. This is useful for systems where you want to avoid having third-party code run.

### `monitor`

The monitor section is useful if you wish to enforce a global trigger limit on organisation and user quotas. this feature will trigger a webhook event to fire when specific triggers are reached. Triggers can be global (set in the node), by organisation (set in the organisation session object) or by key (set in the key session object).

While Oganisation-level and Key-level triggers can be tiered (e.g. trigger at 10%, trigger at 20%, trigger at 80%), in the node-level configuration only a global value can be set. If a global value and specific trigger level are the same the trigger will only fire once:

	"monitor": {
        "enable_trigger_monitors": true,
        "configuration": {
        	"method": "POST",
            "target_path": "http://domain.com/notify/quota-trigger",
            "template_path": "templates/monitor_template.json",
            "header_map": {"some-secret": "89787855"},
            "event_timeout": 10
        },
        "global_trigger_limit": 80.0,
        "monitor_user_keys": false,
        "monitor_org_keys": true
    },

### `monitor.enable_trigger_monitors`

Set this to true to have monitors enabled in your configuration for the node

### `monitor.configuration.method`

The method to use for the webhook

### `monitor.configuration.target_path`

The target path on which to send the request

### `monitor.configuration.template_path`

The template to load in order to format the request

### `monitor.configuration.header_map`

Headers to set when firing the webhook

### `monitor.configuration.event_timeout`

The cool-down for the event so it does not trigger again (in seconds)

### `monitor.global_trigger_limit`

The trigger limit, as a percentage of the quota that must be reached in order to trigger the event, any time the quota percentage is increased the event will trigger.

### `monitor.monitor_user_keys`

Apply the monitoring subsystem to user keys

### `monitor.monitor_org_keys`

Apply the monitoring subsystem to organisation keys


### `db_app_conf_options`

this section defines API sharding options, enable these settings to only selectively load API definitions on a node.

#### `db_app_conf_options.node_is_segmented`

Set to true to enable filtering of APIs

#### `db_app_conf_options.tags`

The tags to use when filtering the nodes, tags are processed as OR operations. If you include a non-filter tage (e.g. an identifier such as `node-id-1`, this will become available to your analytics dashboard)
