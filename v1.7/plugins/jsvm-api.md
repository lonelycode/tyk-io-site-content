+++
draft = false
title = "Tyk JavaScript API"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_7]
    parent = "plugins"
    weight = -80
+++

### Overview

Embedded Javscript interpreters offer the bre bones of a scripting language, but not necessarily the functions that you would expect, esecially with JavaScript, where objects such as XMLHttpRequest() are a given. However, thse interfaces are actually provided by the browser / DOM that the script engine are executing in. In a similar vein, we have included a series of functions to the JSVM for convenience and give the interpreter more capability.

This list is regularly revised and any new suggestions should be made in our [Github issue tracker](https://github.com/lonelycode/tyk/issues).

below is the list of functions currently provided by Tyk.

### `log(string)`

Calling `log("this message")` will cause Tyk to log the string to Tyk's default logger output in the form `JSVM Log: this message` as an INFO statement. This function is especially useful for debugging your scripts, it is recommended to put a `log()` call at the end of your middleware and event handler module definitions to indicate on load that they have been loaded succesfully - see the example scripts supplied with Tyk for more.

### `TykMakeHttpRequest(JSON.stringify(requestObject))`

This method is used to makee an HTTP request, requests are encoded as JSON for deserialisation in the min binary and translation to a system HTTP call. The request object has the following structure:

    newRequest = {
        "Method": "POST",
        "Body": JSON.stringify(event),
        "Headers": {},
        "Domain": "http://foo.com",
        "Resource": "/event/quotas",
        "FormData": {"field": "value"}
    };

Tyk passes a simplified response back which ooks like this:

    type TykJSHttpResponse struct {
        Code int
        Body string
        Headers map[string][]string
    }
    
The response is JSON string encoded, and so will need to be decoded again before it is usable: 

    usableResponse = JSON.parse(response);
    log("Response code: " + usableResponse.Code);
    log("Response body: " + usableResponse.Body);

This method does not execute asyncoronously, so execution will block until a response is received.

### `TykGetKeyData(api_key, api_id)`

Use this method to retrieve a session object for the key and the API provided:

    // In an event handler, we can get the key idea from the event, and the API ID from the context variable.
    var thisSession = JSON.parse(TykGetKeyData(event.EventMetaData.Key, context.APIID))
    log("Expires: " + thisSession.expires)

### `TykSetKeyData(api_key, api_id)`

Use this method to write data back into the Tyk session store:

    // You can modify the object just like with the REST API
    thisSession.expires = thisSession.expires + 1000;
    
    // Use TykSetKeyData to set the key data back in the session store
    TykSetKeyData(event.EventMetaData.Key, JSON.stringify(thisSession));

All of these methods are described in functional examples in the tyk `middleware/` and `event_handlers/` folders.
