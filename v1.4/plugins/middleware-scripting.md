+++
draft = false
title = "Middleware Scripting"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_4]
    parent = "plugins"
    weight = -95
+++

### Overview

Middleware scripting is done in either a PRE or POSt middleware chain context. The difference between these are:

1. `PRE` - These middleware instances do not have access to the sesison object (as it has not been created yet) and therefore cannot perform modification actions on them.

2. `POST` - these middleware components have access to the session object (the user quota, allowances and auth data), but have the option to disable it, as deserialising it into the JSVM is computationaly expensive and can add latency. 

It is important to note that a new JSVM instance is created for *each* API that is managed, this means that inter-API communication is not possible via shared methods (they have different bounds), however it *is* possible using the session object if a key is shared across APIs.

### Creating a middleware component

Tyk injects a TykJS namespace into the JSVM, this namespace can be used to initialise new a midleware component. Each middleware component should be in it's own `*.js` file.

Creating a middleware object is done my calling the `TykJS.TykMiddleware.NewMiddleware({})` constructor with an empty object and then initialising it with your function using the `NewProcessRequest()` closure syntax.

Here is an example implementation:

    /* --- sample.js --- */
    
    // Create your middlware object
    var sampleMiddleware = new TykJS.TykMiddleware.NewMiddleware({});

    // Initialise it with your functionality by passing a closure that accepts two objects 
    // into the NewProcessRequest() function:
    sampleMiddleware.NewProcessRequest(function(request, session) {
        
        log("This middleware does nothing, but will print this to your terminal.")
        
        // You MUST return both the request and session metadata    
        return sampleMiddleware.ReturnData(request, session.meta_data);
    });
    
### Middleware component variables

As well as the [API functions](plugins/jsvm-api/) that all JSVM components share, the middleware components have access to some data structures that
are performant and allow for the modification of both the request itself and the session. These objects are exposed to the middleware in the form of the `request` and `session` objects in the `NewProcessRequest(function(request, session) {};` call.

In the example above, we can see that we return these variables - this is a requirement, and omiting it can cause the middleware to fail, this line should be called at the end of each process:

    return sampleMiddleware.ReturnData(request, session.meta_data);
    
This allows the middleware machinery to perform the necessary writes nd changes to the two main context objects.

#### The `request` object

The `request` object provides a set of arrays that can be manipulated, that when changed, will affect the request as it passes through the middleware pipeline, the `request` object looks like this:

    {
        Headers       map[string][]string
        SetHeaders    map[string]string
        DeleteHeaders []string
        Body          string
        URL           string
        AddParams     map[string]string
        DeleteParams  []string
    }
    
- **`Headers`**:  this is an object of string arrays, and represents the current state of the request header. This object cannot be modified directly, but can be used to read header data.
- **`SetHeaders`**: This is a key-value map that will be set in the header whn the middleware returns the object, existing headers will be overwritten and nw headers will be added.
- **`DeleteHeaders`**: Any header name that is in this list will be deleted from the outgoing request. Deletes happen before Sets.
- **`Body`**: This represents the body of the request, if you modify this field it will overwrite the request
- **`URL`**: This represents the path -portion of the outpbound URL, use this to redirect a URL to a different endpoint upstream
- **`AddParams`**: You can add parameters to your request here, for example internal data headers that are only relevant to your network setup
- **`DeleteParams`**: These parameters will be removed from the request as they pas through the middleware. Deletes happen before Addition.

Using the methods outlined above, alongside the API functions that are made available to the VM, allows for a powerful set of tools for shaping and structuring inbound traffic to your API, as well as processing, validating or re-structuring the data as it is inbound.

#### The `session` object

Tyk uses an internal sesison representation to handle the quota, rate limits, and access allowances of a specific key. This data can be made available to
POST-processing middleware for processing. the sesison object itslef cannot be edited, as it is crucial to the correct functioning of Tyk. 

In order for middleware to be able to transfer data between each other, the session object makes availale a `meta_data` key/value field that is written back to the session store (and can be retrieved mby the middleware down the line) - this data is permanent, and can also be retreived by the REST API from outside of Tyk using the `/ty/keys/` method.

The session object has the same representation as the one used by the API:

    {
        "allowance": 999,
        "rate": 1000,
        "per": 60,
        "expires": 0,
        "quota_max": -1,
        "quota_renews": 1406121006,
        "quota_remaining": 0,
        "quota_renewal_rate": 60,
        "access_rights": {
            "234a71b4c2274e5a57610fe48cdedf40": {
                "api_name": "Versioned API",
                "api_id": "234a71b4c2274e5a57610fe48cdedf40",
                "versions": [
                    "v1"
                ]
            }
        },
        "org_id": "53ac07777cbb8c2d53000002",
        "meta_data": {
            "your-key": "your-value"
        }
    }
    
There are other ways of accessing and editing a session object by using the Tyk JSVM API functions.
    
### Hooking up the middleware component

In order to activate this iddleware, it needs to be rgistered as part of your API Definition, registration of middleware components is relatively simple, *it is important that your object names are unique*.

Adding the middleware plugin is as simple as adding it to your defintiion file in the middleware sections:

    // sample_api.conf
    
    ...
    "event_handlers": {},
    "custom_middleware": {
        "pre": [
            {
                "name": "sampleMiddleware",
                "path": "middleware/sample.js",
                "require_session": false
            }
        ],
        "post": [
            {
                "name": "sampleMiddleware",
                "path": "middleware/sample.js",
                "require_session": false
            }
        ]
    },
    "enable_batch_request_support": false,
    ...
    
As you can see, the parameters are all dynamic, so you will need to ensure that the path to your middleware is correct, the configuration sections here are as follows:

#### `pre`

Defines a list of custom middleware objects to run *in order* from top to bottom. That will be executed *before* any authentication information is extracted from the header or parameter list of the request. Use middleware in this section to pre-process a request before feeding it through the Tyk middleware. 

#### `pre[].name`

The name of the middleware object to call. This is case sensitive, it should match the name of the middleware object that was created, so in our example - we created `sampleMiddleware` by calling: `var sampleMiddleware = new TykJS.TykMiddleware.NewMiddleware({});`

#### `pre[].path`

The path to the middleware compoenent, this will be loaded into the JSVM when the API is initialised. This means that if you reload an API configuration, the middleware will also be re-loaded. Meaning you can hot-swap middleware on reload with no service interuption.

#### `pre[].require_session`

Irrelevant for pre-processor middleware, since no auth data has been extracted by the authentication middleware, it cannot be made available to the middleware.

#### `post`

Defines a list of custom middleware objects to run *in order* from top to bottom. That will be executed *after* the authentication, validation, throttling, and quota-limiting middleware has been executed, just before the request is proxied upstream. Use middleware in this section to post-process a request before sending it to your upstream API. 

#### `post[].name`

The name of the middleware object to call. This is case sensitive, it should match the name of the middleware object that was created, so in our example - we created `sampleMiddleware` by calling: `var sampleMiddleware = new TykJS.TykMiddleware.NewMiddleware({});`

#### `post[].path`

The path to the middleware compoenent, this will be loaded into the JSVM when the API is initialised. This means that if you reload an API configuration, the middleware will also be re-loaded. Meaning you can hot-swap middleware on reload with no service interuption.

#### `post[].require_session`

Defaults to false, if you require access ot the session object, it will be supplied as a `session` variable to your middleware processor function.