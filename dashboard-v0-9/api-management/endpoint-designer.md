
+++
draft = false
title = "Endpoint Designer"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -180
+++

The endpoint designer is a powerful and versatile way for you to add specific behaviors to your API. By Default, Tyk will proxy all traffic through the listen path that you have defined. 

In the case that you want to have specific behaviors applied to a path (for example, header injection), then you can enable the middleware on a path-by-path basis by using matching patterns in the endpoint designer.

**Note:** you do not need to define your whole APi in the editor, only those paths you want to manage. The exception to this is if you are using a white-list, in which case you will need to specify every endpoint as all others will be blocked.

By default, importing an API using Swagger or API Blueprint definitions will generate a white-list.

![API Manager - Endpoint Designer](/imgs/endpoint-designer.png)

To get started, click the "Add Endpoint" button, this will give you an empty path definition:

![API Manager - New path](/imgs/new-path.png)

In a new path definition, you can set multiple options, and if you do not specify a specific action for that list (from the plugins drop-down), then saving the path will actually do nothing (and it will vanish).

The options here are:

- **Method**: The method you are targeting, can be any valid HTTP method, simply pick one from the drop-down menu
- **Path**: The path to target - it is important to exclude aberrant slashes (`/`) from your path matching, as otherwise the gateway may not match the path correctly. A path can contain wild cards, such as `{id}`, the actual value in the wildcard is not used (it is translated into a regex), however it is useful to make the path more human readable when editing.
- **Plugin**: A path can belong to multiple plug-ins, these plug-ins define the behaviour you want to impose on the matched request. there are many plug-ins, we've briefly outlined them in the "plugins" section, but would suggest looking at the detailed documentation in the main Tyk docs for detailed information on how these plug-ins function.

### Versions

At the top of the endpoint designer you can see which version you are currently editing. If you ahve more thanone option, selecting it from the drop-down will load it's endpoint configuration into the editor.