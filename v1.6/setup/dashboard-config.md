+++
draft = false
title = "Dashboard configuration file"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_6]
    parent = "setup"
+++

The Tyk Dashboard has a seperate configuration file, it is small and comes packaged with the tarball. Tyk Dashboard uses
a seperate configuration file as it may be installed on a different host to your Tyk nodes. 

The dashboard configuration file can be found in the tyk-dashboard folder and is usually called `tyk_analytics.conf` though
it can be renamed and specified using the `--conf` flag.

The file will look like the sample below, the various fields are explained in the followin sections:

    {
        "listen_port": 3000,
        "tyk_api_config": {
            "Host": "http://localhost",
            "Port": "5000",
            "Secret": "352d20ee67be67f6340b4c0605b044b7"
        },
        "mongo_url": "mongodb://localhost/tyk_analytics",
        "page_size": 10,
        "admin_secret": "12345",
        "redis_port": 6379,
        "redis_host": "localhost",
        "redis_password": "test",
        "force_api_defaults": true,
        "notify_on_change": true,
        "hash_keys": false
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

The page size that the dashboard shoul duse, defaults to `10` should not be edited.

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

### `force_api_defaults`

Forces the dashboard to use certain defaults when generating API definitions.

### `notify_on_change` 

Licensed users can use this setting to enable/disable whether Tyk Dashboard will notify all tyk nodes to hot-reload when 
an API definition is changed.

### `license_owner`

Deprecated, licenses are no long required to use the dashboard

### `hash_keys`

If your Tyk gateway is using hashed tokens, set this value here to `true` so it matches, the dashboard will now operate in a mode that is 
compatible with key hashing

