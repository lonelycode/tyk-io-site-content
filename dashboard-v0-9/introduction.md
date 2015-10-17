+++
draft = false
title = "The Tyk Dashboard"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -1000
+++

### Overview

The Tyk Dashboard offers a platform for you to view, analyse and track how your APIs are performing, as well as a management interface that lets you:

1. Manage users with access to the dashboard
2. Manage APIs that are loaded by your tyk nodes
3. Manage webhooks that are used by your APIs to communicate messages, events and actions to your other infrastructure
4. Manage your API Portal pages, developers and key requests
5. Manage API portal catalogue and associated documentation (Swagger or API Blueprint markdown)

### Working together with Tyk

The Dasboard has multiple modes of operation depending on how you are working with tyk, if you have Tyk nodes that are using file-based API Definitions, then the dashboard can only provide analytics support. If you have configured your Tyk nodes to use a database-backed configuration, then Tyk Dashboard is fully operational, with key and API management becoming active.

This suport section primarily deals with the second use case - that of a DB-backd Tyk management structure. The benefits behind a centralised storage solution using the database is:

1. Central storage of your API defintions
2. Allows multiple nodes in a larger coud environment to synhronise an load API Definitions
3. Enables hot-reloading an entire cluster of tyk nodes when a change is pushed to the database

### Components

A full Tyk install that deploys the dashboard will consist of:

- Tyk Nodes: to manage your APIs, providing a security layer and gateway to upstream services
- Host managers: Installed alongside your Tyk nodes, these manage domain-level routing using NginX (not required)
- Redis: The primary key store
- MongoDB: The primary analytics store, Tyk will purge analytic into MongoDB periodically
- The Advanced Management API: This is a more granular, structured, secure and overall advanced API to drive a Tyk cluster
- The Dashboard: an API client that provides a GUI to your management functions and analytics

This set up enables a distributed, orchestrated cluster of Tyk nodes to manage your upstream APIs and a centralised (scalable) command and control API that provides granular, user-baed administrative access to an organisations APIs. The full Tyk stack provides a platform for managing multi-owner, multi-tenant API structure wiht a friendly UI.

#### Tyk stack diagram

This is how the Tyk stack fits together, the Tyk node processes and their relevant host managers represent a host running multiple Tyk processes:

![Tyk Stack](/imgs/full-tyk-stack.jpg)

**Note:** The host manager is no longer requiresd to manage hot-reloads, Tyk clusters will hot-reload themselves by directly hooking into the fanout pub/sub channel. This is now the default behaviour of a Tyk node / host manager setup)

#### The Database

Tyk stores it's analytics data in MongoDB, and MongoDB 2.6+ is a requirement for analytics to function correctly. Tyk Dashboard uses the Advanced Management API to store and manage configurations in this database. Tyk nodes will pull ocnfigurations from the DB when a reload or looad event occurs.

#### The Dashboard

The Dashboard is an API client and speaks directly to a Tyk node and to the Advanced Management API, it provides a graphic user interface to these functions as well as a graphical view of your analytics data, enabling you to drill-down into API poerformance according to various factors.

#### Management API

A common question around using a databse-backed configuration is how to programatically add API definitions to your Tyk nodes, the dashboard comes bundled with the Advanced Management API (and is in itself a client of this API), our Advanced API allows much more fine-grained, secure and multi-user access to your Tyk cluster, and should be used to manage a database-backed Tyk node.

The full API is documented seperately in the Advanced Management API section of the site.

#### The Host Manager

Tyk dashboard comes bundled with another utility called `host_manager`, this utility can be installed alonside your Tyk Nodes and configured to monitor the advanced API, when a change in the API Configurations is detected, the host manager will hot-reload any Tyk Nodes it i onfigured to monitor on a host. This means that the host manager, alongside tyk, can be used to orchestrate changes to your API gateway infrastructure concurrently.

