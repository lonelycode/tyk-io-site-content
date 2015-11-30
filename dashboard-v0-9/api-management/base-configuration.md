+++
draft = false
title = "Base configuration"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -200
+++

The base configuration panel is all you need to get started with Tyk and get an API live in your cluster. To get started, lets look at the first panel - "API Settings":

### API settings

This panel lets you edit the main settings for an API, the fields are described below:

- **API Name**: the human readable name of your API, this is for your reference only, it is not used by Tyk in any way except display purposes
- **API Slug**: The API slug defines the machine-name of the API to use, this slug is used by the host-manager templates to define the URL of the new API (if enabled)
- **Target URL**: The target host to proxy requests to. 
- **Enable load balancing**: Whether multiple upstream targets should be used, if enabled, a new panel will appear enabling you to enter more than one path
- **Listen Path**: This may not always be editable, but this is the path that your API will "listen on" on the tyk cluster. If you are using the host manger, then this will be masked with the api-slug in some form of domain configuration
- **Custom Domain**: Enter the domain name yo would like Tyk to bind the API to, APIs are bound to domains as: `domain_name/listen_path`. When using a custom domain, the api-slug is not used as it only applies to Host-Manager managed installations.

### Service Discovery

Tyk nodes now support service discovery modes in an API, in service discovery mode and API will search for upstream target information from a service discovery service such as Etcd, Consul or Eureka. In fact, any service discovery or configuration management tool that exposes a RETful interface can be queried for host data.

The options available are:

- **Enable Service Discovery**: Set this option to enable the feature
- **Query Endpoint**: The endpoint URL to query in your servie discovery system
- **Are the values nested**: In some cases, the value field in a k/v store will be a string-encoded JSOn object, which itself will need decoding, if you enble this option, you will be able to enter the "parent data path" that points at the nested value, an then use the data path to unpack it
- **Data Path**: The namespace of the hostname(s) in dot-notation - e.g. `object.key.property1`
- **Is port information separate from host name?**: In some cases you will have two namespaces, one for the host-name and the other for the port data, if this is the case, enabling this option will give you another namespace to use for the port data
- **Does the endpoint provide a target list?**: If you have enabled load-balancing, then you can enable this option to have the namespace treated as a list
- **Cache timeout**: How long to cache the service data before refreshing it.

### Target details

This section outlines how your API will be secured, there are multiple flows available:

1. **Authentication mode**: This dropdown allows you to set the authentication mode that your API should use, this can be one of:
    - *Auth Token*: For standard token-based
     APIs
    - *HMAC*: This will enable a standards-compliant HMAC mode (please see the relevant section in the Tyk Core documentation regarding HMAC support)
    - *OAUth 2.0*: This will enable one of two OAuth 2.0 flows (please see the relevant section in the Tyk Core documentation regarding OAuth 2.0 Flows)
    - *Basic Auth*: This will enable basic Authentication for your APi (a good way to test authentication and throughput)
    - *JSON Web Token* - Validate JWT tokens using specified subject or kid's using RSA or HMAC cryptographic signatures
    - *Open (Keyless)*: This will make the API be open, great for debugging, however Open APIs record less data and cannot enforce throttling

2. **Active**: Change this value to make the API active or inactive, inactive APIs will not be loaded by your Tyk nodes.
3. **Use Query Parameter Instead Of Header**: By default Tyk will look for an authentication token in a header, check this option to make Tyk look for an auth token in the query string / form of the request
5 **Use Cookie Value**: Set this option to use a cookie name instead to look for the auth token

