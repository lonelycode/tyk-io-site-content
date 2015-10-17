+++
draft = false
title = "Configuration"
date = 2014-07-29T10:54:19Z
[menu.sidebar]
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


