+++
draft = false
title = "Defining an API"
date = 2014-07-29T10:56:22Z
[menu.sidebar]
    parent = "management"
    weight = -100
+++

This is a high-level overview of how APIs are defined in tyk - for a detailed breakdown of each option in the API definiton object, please see the 
[detailed breakdown](/api-management/api-definition-detail/) section of this guide.

Tyk can handle different access scenarios for multiple APIs in a single instance. It does this through the provision of API Definitions, these become handlers for 
a specific root-path (`listen_path` in the configuration) and proxy every request into this root path to an outward host.

The way a request is handled with tyk and bound to a spec is as follows:

1. Request comes to host
2. URL Router matches `listen_path`
3. If this path has a handler (an API Definition), start the authentication chain
    - If the request is keyless, skip to 4.
    - Check if key exists for authentication type (Auth token, Basic auth username, OAuth2 Access token or valid HMAC header and token)
    - Get session data for key if it exists
    - Check if key has expired (key may exist, but has expired field set)
    - Check if key has access to API version
    - Check rate limits and quota
4. If analytics is enabled, log success or failure of the request
5. If none of the gates failed, pass the request through and update the session

Every request will go through this pattern as it hits the system, with quota and rate limiting data being updated continuously.

Within an API definition, each element of this chain can be configured or activated.

## Definition files

Definitions take the form of files stored in the `/etc/tyk/apps/` folder, on startup Tyk will look for JSON files in this folder, and if it finds them 
it will attempt to load a configuration form them. Configurations that are marked as active will be loaded into the Tyk routing infrastructure.

Definition objects have the following structure - not all keys are required:

    {
        "name": "Tyk Test API",
        "api_id": "1",
        "org_id": "default",
        "definition": {
            "location": "",
            "key": ""
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
    
For full details on how the varying fields in this configuration file work please see the [detailed breakdown](/api-management/api-definition-detail/) section of this guide.

## Hot-configuration reload

It is possible to change an API configuration and reload that configuration into Tyk without having to stop the service. this is accomplished through hitting an enpoint in the 
Tyk REST API:

    martin@vyr ~/tyk/> curl -H "X-Tyk-Authorization: your-secret-key-here" http:///tyk-instance.com:port/tyk/reload
    
If you can see the output screen of your Tyk instance you will see that it has reloaded the Definitions, changes take effect immediately.

It is possible to restart the Tyk instance with near-zero downtime by sending the process a SIGUSR2 signal though the above method is preferred for updating or addding new API defintitions to a running setup.

If you are running tyk in a load balanced way (e.g. round-robin via NGinX) then you will need to send this request to each process.



