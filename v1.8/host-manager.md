+++
draft = false
title = "Host Manager"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_8]
    weight = -100

+++

The host manager is a utility that helps you manage Tyk reloads (deprecated - now integrated into Tyk Gateway) and to manage your
NGinX configuration and server.

APIs managed by Tyk tend to live behind long, obscure IDs and have unfriendly URLs, e.g.:

	http://gateway.domain.com:8080/3b7e73fd18794f146aab9c2e07b787bf/widgets

In order to get around this, you can set up NGinX to reverse proxy friendly subdomains such as api.domain.com to Tyk by
the API ID (that long number in the first URL).

If you have a rapidly changing API landscape, or are running Tyk with with multiple teams that each have their own APIs and are
running many of them at once, then you will want to automate subdomain management just for simplicities sake.

This is what the host manager is good for, it has a series of site templates (similar to those found in sites-enabled of your nginx
setup) that it will generate for each API that it finds when a change occurs to your Tyk Gateway definitions.

### Configuration

The configuration of the host manager is very straightforward:

	{
	    "mongo_url": "mongodb://localhost/tyk_analytics",
	    "redis_port": 6379,
	    "redis_host": "localhost",
	    "redis_password": "",
	    "tyk_ports": [8080],
	    "tyk_secret": "352d20ee67be67f6340b4c0605b044b7",
	    "manage_nginx": true,
	    "manage_tyk_nodes": false,
	    "db_app_conf_options": {
	        "node_is_segmented": false,
	        "tags": []
	    }
	}

The Mongo, Redis and Tyk configurations should match your `tyk.conf` setup for the main gateway. This is because the host manager needs to know
when an update has occurred (redis), the API's that have changed (mongo) and the tyk configuration (to notify Tyk if it is managed, to reload).

You should run the host manager as sudo as it will try to hot-reload the NginX server using a services command.

In order to get things to work, you will need to symlink your sites-enabled folder with the `nginx_confs` folder that comes with the host
manager binary. This is where Tyk will write the API site definitions.

### Tags

If you enable the `node_is_segmented` option then the host manager will only download API definitions that are in your tag groups, tags are selected as non-exclusive "OR" operations, so if any of them match a given API tag set, they will be loaded.

This feature can be handy when distinguishing upstream APIs by a segment, client or geography.

### Templates

There are two templates that you can use, the first is for an API definition, and the second is for your Portal (if you are running one).

All templates use Golangs template language (similar to handlebars), the examples included in the folder assume that you have set up Tyk
and the gateway as an upstream host in your main nginx configuration as follows:

	http {
	    # Enumerate all the Tyk servers here
	    upstream tyk {
	        server 127.0.0.1:8080;
	    }
	    upstream dashboard {
	        server 127.0.0.1:3000;
	    }
	...

The individual templates will then take care of the actual redirects.

#### Variables

There are specific template variables that are included with the templates for convenience, these are outlined below:

* `.Slug` - The API Slug of your API, this can be set in the API Definition under the `slug` flag.
* `.OwnerSlug` - A slug for the organisation that owns the API, this must be set when the organisation is created using the command line when setting up the dashboard OR by using the organisations API endpoint in the advanced API.
* `.ApiId` - THE ID of the generating API, this is required for the proxy URL
* `.CNAME` - A CNAME (if available), this needs to be set in the `tyk_organisations` collection in MongoDB and can also be set using the Tyk REST API

If you examine the example files, you will see how these have been applied in order to generate usable configurations for NginX.

### Subdomain-first or path-based API routing

The templates included with the host manager include two ways of handling domains, in some cases having APIs segregated by subdomain is desirable:

- `api-1-name.domain.com/resource/id`
- `api-2-name.domain.com/resource/id`

In other cases this isn't feasible, for example if you are trying to expose a rational facade of API endpoints that actually hide multiple disparate underlying APIs. In this case you probably want:

- `apis.domain.com/api-1-name/resource/id`
- `apis.domain.com/api-2-name/resource/id`

Both options are possible, the first is represented in the `nginx.conf` template and the latter is demonstrated in the `portal.conf` template.


-
