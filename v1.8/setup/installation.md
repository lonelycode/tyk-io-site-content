+++
draft = false
title = "Manual installation"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
    weight = -300
+++

Getting started with tyk is a straightforward task - head on over to our downloads page and download the binary that is 
most appropriate for your distribution. Currently the builds ae available for Linux on amd64, i386 and ARM architectures. Only amd64 have been thoroughly tested.

## Step 1: Download the binaries

### Redis
Before anything will work, please install Redis - this can either be on the same system, or on another server. If you just want to try out Tyk, the quickest thing 
to do is just install Redis, instructions on how to do this can be found on the [Redis documentation pages](http://redis.io/topics/quickstart). You may
also be able to install redis via your package manager.

### Debian packages 

If you are using one of our debian packages - you can use `dpkg` to install the application using:

    martin@vyr ~/> sudo dpkg -i tyk.linux.amd64_1.6-1_all.deb
    
This will install the `tyk` binary to your /usr/local/bin directory and place all configuration files into `/etc/tyk`

Skip on to step 2 now to ensure that things are configured correctly to get up and running.

### Tarballs

If you are using the tarballs, copy them to a new folder and run:

    martin@vyr ~/tyk/> tar xvzf tyk.linux.amd64_1.6-1_all.tar.gz

This will extract the files to a local directory. Tyk will work from anywhere and only expects (by default) to find configuration
files in the `/etc/tyk` folder.

#### Step 1b (For tarballs only)

Since tyk expects to find templates and app configurations in the `/etc/tyk` folder, and if this is not set up (or you don't have access to it), 
you can override those settings.

First edit the tyk.conf file and find the entries that read:

    template_path: "/etc/tyk/templates"
    
And replace that with:

    "template_path": "templates",
    "app_path": "apps",
    
This will get Tyk to look in the local directory for the template and app files (these folders should come with the tarball).
Note you can set this to any directory you like.

### Step 2: Initial configuration

The main things that need ot be set up is to tell Tyk how to find the Redis instance, and secondly, what to proxy. Tyk ships with 
an example configuration file - and to get up and running as quickly as possible - you only need to modify the following sections
to point at your redis instance, this is in the 'storage' section:

Modify the `storage` section with your Redis details:

    "storage": {
        "type": "redis",
        "host": "localhost",
        "port": 6379,
        "username": "",
        "password": ""
    },
    
### Step 3: Define an API

Tyk stores APIs in things called Definitions - these are either stored in MongoDB (more on that elsewhere) or as flat-files on disk. If you open up
`/etc/tyk/apps/app_sample.json` it will look something like this:

    {
        "name": "Tyk Test API",
        "api_id": "1",
        "org_id": "default",
        "definition": {
            "location": "header",
            "key": "version"
        },
        "auth": {
            "auth_header_name": "authorization"
        },
        "version_data": {
            "not_versioned": true,
            "versions": {
                "Default": {
                    "name": "Default",
                    "expires": "3000-01-02 15:04",
                    use_extended_paths: true,
                    "extended_paths": {
                        "ignored": [],
                        "white_list": [],
                        "black_list": []
                    }
                }
            }
        },
        "proxy": {
            "listen_path": "/tyk-api-test/",
            "target_url": "http://example.com"
        }
    }

To get things started quickly, lets create an open-access API (Keyless), in order to do this we need to set 
the `use_keyless` flag to true, and clear the `auth` section:

    {
        "name": "Tyk Test API",
        "api_id": "1",
        "org_id": "default",
        "definition": {
            "location": "header",
            "key": "version"
        },
        "use_keyless": true,
        "auth": {
            "auth_header_name": ""
        },
        "version_data": {
            "not_versioned": true,
            "versions": {
                "Default": {
                    "name": "Default",
                    "expires": "3000-01-02 15:04",
                    "paths": {
                        "ignored": [],
                        "white_list": [],
                        "black_list": []
                    },
                    use_extended_paths: true,
                    "extended_paths": {
                        "ignored": [],
                        "white_list": [],
                        "black_list": []
                    }
                }
            }
        },
        "proxy": {
            "listen_path": "/quickstart/",
            "target_url": "http://httpbin.org/",
            "strip_listen_path": true
        }
    }

The most crucial part of an app config to edit is the `target_url`, in our example it is set to: `http://jive.ly`, you can 
leave it like that if you want, but making requests to will basically cause you to query our company home page! 
For the purposes of this setup guide, let's set this to: `http://httpbin.org/`, httpbin is a breat service that will 
echo back your request parameters as a JSON object and has a variety of endpoints that can be tested.

You will also have noticed we added a new parameter to the `proxy` section: `strip_listen_path` this prevents requests to 
be proxied with the listen path intact (i.e. in this case `/quickstart/get/this` will be tripped back to `/get/this`)
 
### Step 4: Run Tyk

Running tyk is very simple, simply invoke it from the command line like so:

    martin@vyr ~/> tyk
    
if you are runnig form a tarball or have installed into a different directory - specify the configuration file that we set up above:

    martin@vyr ~/tyk/> ./tyk --conf=tyk.conf

If everything has ben configured correctly, you should now see some output that looks like the below:

    INFO[0000] Using tyk.conf for configuration
	INFO[0000] Listening on port: 8080
	INFO[0000] Listening on [::]:8080
	INFO[0000] Loading API Specification from apps/app_sample.json
	INFO[0000] Checking for transform paths...
	INFO[0000] Loading API Specification from apps/quickstart.json
	INFO[0000] Checking for transform paths...
	INFO[0000] Loading API configurations.
	INFO[0000] Loading API Spec for: Tyk Test API
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Health Checker initialised.
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Loading Middleware
	INFO[0000] Batch requests enabled for API
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Rate limits available at: /tyk-api-test/tyk/rate-limits/
	INFO[0000] Loading API Spec for: Tyk Test API
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Health Checker initialised.
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Connecting to redis on: localhost:6379
	INFO[0000] Loading Middleware
	INFO[0000] Loading policies
	INFO[0000] No policy record name defined, skipping...
    
Don't worry about all those Redis connections, Tyk uses a large pool to ensure that we can quickly get data in an out of Redis and limit request round-trip time.

You should now be able to run the following and get a JSON response back form the httpbin service:

    martin@vyr ~/> curl http://localhost:5000/quickstart/get
    {
      "args": {}, 
      "headers": {
        "Accept": "*/*", 
        "Accept-Encoding": "gzip", 
        "Connection": "close", 
        "Host": "httpbin.org", 
        "User-Agent": "curl/7.35.0", 
        "X-Request-Id": "22fa662e-0146-4cce-809b-137923946891"
      }, 
      "origin": "x.x.x.x, x.x.x.x", 
      "url": "http://httpbin.org/get"
    }

### Ready for more? ###

**Next**: Head on over to the [configuration](../../v1.6/setup/confguration) section to see a breakdown of all of the options in the configuration file.