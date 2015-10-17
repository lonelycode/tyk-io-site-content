+++
draft = false
title = "Versioning"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -200
+++

Tyk enables full version lifecycle managment for your APIs, it includes the ability to define different configurations for different versions of an API and hhave Tyk manage route and middleware configurations on the same listen path of any given API. By default, Tyk expects to find version information in a header key or in a query parameter. **Note:** Tyk does not support path-based version detection.

![API Manager - version settings](/imgs/api-version-settings.png)

Versioning can either be enabled or disabled, if an API is not versioned, a "Default" version is created whih can store any path-related data and settings. Versions are set by a unique version name that matches the version tag you use to identify the version in a request, this can either be as a header field or as a parameter.

All versions have an expiry date, set this to emtpy for the version to never expire. 

The versions fields define the value that Tyk expects to find either in the versions headers or query parameter, so if your version name is `v1` then tyk will look for a header key that matches `v1` exactly on the key that you specifiy (default `version`).