+++
draft = false
title = "Directory-based middleware loading"
date = 2014-07-29T10:58:44Z
[menu.sidebar_v1_9]
    parent = "plugins"
    weight = -90
+++

In some cases middleware references can't be directly embedded in API Definitions (for example, when using the dashboard). However this does not mean that there isn't an easy way to distribute and enable custom middleware for an API on a Tyk node.

A second method of loading API Definitions in Tyk nodes is to add them as a directory structure in the Tyk node, Tyk will load the middleware plugins dynamically on host-reload without needing a direct reference to them in the API Definition.

The URL structure looks like this:

	middleware/
	---- {APIID}
	-------- pre/
	------------ middlewareObject1Name.js
	------------ middlewareObjcet2Name.js
	-------- pst/
	------------ middlewareObject1Name_with_session.js
	------------ middlewareObjcet2Name.js

Tyk will check for a folder that matches the API ID being loaded, and then load the `pre` and `post` middleware from the respective folders. The filename *must* match the object to be loaded exactly. 

If your middleware requires session injection, then append `_with_session` to the filename.

