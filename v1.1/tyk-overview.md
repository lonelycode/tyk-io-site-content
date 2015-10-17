+++
draft = false
title = "Tyk Overview"
date = 2014-07-29T09:31:36Z
[menu.sidebar]
    weight = -300
    
+++

Tyk is only one single component - the tyk application bundle. This can be installed as a package via our DEB installer, or can be 
downloaded and added to your path manually.

At it's core, Tyk is a reverse proxy, accepting a request on your APIs behalf, gating the request through a series of 
access control middleware, tracking the access and then forwarding it onto your service.

You might think this adds latency to your API requests, which it does, but it is marginal considering the amount of control that you get
over what requests actually make it through to your API.

Tyk is traditionally installed behind NGinX as an upstream application - so a request would:

1. Reach NginX, and be load balanced through to a Tyk process
2. The request would then be checked for access control headers
3. Depending on the API being accessed, the appropriate middleware is then activated to gate the request (rate limits, quota's etc.)
4. If all has passed, the request is tagged (if analytics is enabled) and proxied through to your application.

This flow is the same for all requests, except if you are using the OAuth flow, in which case things work a bit differently.

## Defining an API ##

APIs are defined as JSON objects, these can either live in a MongoDB instance or as flat-files in the `/etc/tyk/apps` directory - the definitions are 
pretty self explanatory but are covered n more detail in the "[Defining an API](/api-management/api-defintition)" section.

These definitions are loaded during the application start to set up the routing, and can also be loaded (without downtime) on-the-fly by calling the [`/tyk/reload/`](/rest-api/api-api-definition)
API endpoint. If you are running Tyk in a load-balanced capacity this will need to be applied to all nodes.

## API Keys ##

Tyk supports the generation of API keys, these are generated in various ways and can also be overriden. Every authentication type (except for Keyless) essentially use a 
session-key to map sesison data for specific users.

Your application should use the Tyk REST API to create or invalidate new keys for your users, and store these against their user details to ensure identity management once 
Tyk lets a request through.

Permissions for keys are stored at the key-level in Redis, if your Redis instance goes down without backup or flushing to disk, these keys will be lost and will need to be regenrated.

You can use the Tyk REST API to check key session statuses, existence and update them if necessary.

## API Identification ##

Tyk will identify an API by it's listening path, for example assume that you have set up Tyk to listen to `gateway.yourservice.com/cat-service`,
when a request comes into this endpoint, Tyk will load the respective API definition from memory and begin handling the request as required. 
Because API Definitions are tied to routing paths, it's important to ensure these are unique.

It is advised to make these unique and configure NGinX to rewrite the URL from listening to a sub-domain. An example NGinX server configuration could be:
 
    server {
        listen 8080;
        server_name api.cats.io;
    
        # The Tyk Gateway
        location / {
            rewrite /(.*) /cat-service/$1 break;
    
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://tyk;  #Assume this reverse proxy definition is set up
        }
    }

**Tip:** Using this method, you can enable multiple APIs on the same host (or load balancing) setup and keep your API access details clean and service-specific.

Please see the configuration section of these docs for more detail on how to set up and configure Tyk.


