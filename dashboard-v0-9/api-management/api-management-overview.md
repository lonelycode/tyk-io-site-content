+++
draft = false
title = "Overview"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -280
+++

API Management is at the core of the Tyk Dashboard, and there are extensive options for each API that correspond to the configurations supplied to the file-based definition system, each option in the dashboard can be replicated using the file-based API Definition files, the dashboard makes this management more intuitive and clearer.

The API Designer is structured into multiple panels, each corresponding to an element of an API Definition file. Not all panels are required and to get started quickly only the options in the "Base configuration" panel are required.

The API designer also features a path editor, which will let you manage all of the various feature options such as caching, white-listing, hard-timeouts and circuit breakers. In fact, for any given path this is where the most work would be done.

The advanced options tab gives access to the more unique settings and fine-grained control over the API configuration such as Caching, CORS and API segmentation.

It is also possible to export an API Definition from the dashboard using the "Export" button, this will download the API as a JSON document so that you can use the API Definition in a file-based Tyk node.
