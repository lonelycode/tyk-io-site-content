+++
draft = false
title = "Service Discovery"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_8]
    parent = "management"
    weight = -200
+++

Service discovery is a very useful feature for when you have a dynamically changing upstream service set, for example, you have ten docker containers taht are running the same service, and you are load balancing between them, if one or more fail, most likely a new service will spawn but on a different IP address. Now the Gateway would need to either be manually reconfigured, or, more appropriately, detect the failure and reconfigure itself.

This is what the service discovery module does. 

Service discovery is configured on a per-API basis, and is set up in the API Object under the `proxy` section:

    ```
    "enable_load_balancing": true,
    service_discovery: {
      use_discovery_service: true,
      query_endpoint: "http://127.0.0.1:4001/v2/keys/services/multiobj",
      use_nested_query: true,
      parent_data_path: "node.value",
      data_path: "array.hostname",
      port_data_path: "array.port",
      use_target_list: true,
      cache_timeout: 10
    },


### `service_discovery.use_discovery_service`

Set this to `true` to enable the discovery module

### `service_discovery.query_endpoint`

The endpoint to call, this would be your consul, etcd or eureka K/V store most likely.

### `service_discovery.data_path`

The namespace of the data path, so for example if your service responds with:

    ```
    {
        "action": "get",
        "node": {
            "key": "/services/single",
            "value": "http://httpbin.org:6000",
            "modifiedIndex": 6,
            "createdIndex": 6
        }
    }
    ```

Then your name space would be `node.value`.

### `service_discovery.use_nested_query`

Sometimes the data you are retrieving is nested in another JSON object, e.g. this is how Etcd responds with a JSON object as a value key:

    ```
    {
        "action": "get",
        "node": {
            "key": "/services/single",
            "value": "{\"hostname\": \"http://httpbin.org\", \"port\": \"80\"}",
            "modifiedIndex": 6,
            "createdIndex": 6
        }
    }
    ```
In this case, the data actually lives within this string-encoded JSON object, so in this case, you set the `use_nested_query` to true, and use a combination of the `data_path` and `parent_data_path` (below)

### `service_discovery.parent_data_path`

This is the namespace of the where to find the nested value, in the above example, it would be `node.value`. You would then change the `data_path` setting to be `hostname`, since this is where the host name data resides in the JSON string. Tyk automatically assumes taht the data_path in this case is in a string-encoded JSOn object and will try to deserialise it. 

Tyk will decode the JSON string and then apply the `data_path` namespace to that object in order to find the value.

### `service_discovery.port_data_path`

In the above nested example, we can see that there is a separate PORT value for the service in the nested JSON. In this case youc an set the `port_data_path` value and Tyk will treat `data_path` as the hostname and zip them together (this assumes that the hostname element does not end in a slash or resource identifier such as `/widgets/`).

In the above example, the `port_data_path` would be `port`.

### `service_discovery.use_target_list`

If you are using load_balancing, set this value to true and tyk will treat the data path as a list and inject it into the target list of your API Definition.

### `service_discovery.cache_timeout`

Tyk caches target data from a discovery service, in order to make this dynamic you can set a cache value when the data expires and new data is loaded. Setting it too low will cause tyk to call the SD service too often, setting it too high could mean that failures are not recovered from quickly

We recommend using the SD module in conjunction with the circuit breaker features, as this makes detetion and discovery of failures at th gateway level much more dynamic and responsive.

