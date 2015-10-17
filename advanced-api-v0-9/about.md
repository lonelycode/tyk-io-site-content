+++
draft = false
title = "Advanced Management API"
date = 2014-07-29T09:31:36Z
[menu.advanced_sidebar_v0_9]
    weight = -310
+++

### Overview

The Tyk Advanced Management API offers granular, programmatic access to a centralised database of resources that your tyk nodes can pull from. This API has a dynamic user administrative structure which means the secret key that is used to communicate with your Tyk nodes can be kept secret and access to the wider mnagmeent functions can be managed on a user-by-user and organisation-by-organisation basis.

A common question around using a databse-backed configuration is how to programatically add API definitions to your Tyk nodes, the Advanced API allows much more fine-grained, secure and multi-user access to your Tyk cluster, and should be used to manage a database-backed Tyk node.

The Advanced API works seamlessy with the Tyk dashboard (and the two come bundled together), how to use the dashboard is documented in the Tyk Dashboard section of the site.

### Components

A full Tyk install that deploys the advaned API will consist of:

- Tyk Nodes: to manage your APIs, providing a security layer and gateway to upstream services
- Host managers: Installed alongside your Tyk nodes to manage hot reloads
- Redis: The primary key store
- MongoDB: The primary analytics store, Tyk will purge analytic into MongoDB periodically
- The Advanced Management API: This is a more granular, structured, secure and overall advanced API to drive a Tyk cluster

This set up enables a distributed, orchestrated cluster of Tyk nodes to manage your upstream APIs and a centralised (scalable) command and control API that provides granular, user-baed administrative access to an organisations APIs. The full Tyk stack provides a platform for managing multi-owner, multi-tenant API structure wiht a friendly UI.

#### Tyk stack diagram

This is how the Tyk stack fits together, the Tyk node processes and their relvant host managers represent a host running multiple Tyk processes:

![Tyk Stack](/imgs/full-tyk-stack.jpg)

#### The Database

Tyk stores it's analytics data in MongoDB, and MongoDB 2.6+ is a requirement for analytics to function correctly. Tyk Dashboard uses the Advanced Management API to store and manage configurations in this database. Tyk nodes will pull ocnfigurations from the DB when a reload or looad event occurs.

#### The Host Manager

Tyk dashboard comes bundled with another utility called `host_manager`, this utility can be installed alonside your Tyk Nodes and configured to monitor the advanced API, when a change in the API Configurations is detected, the host manager will hot-reload any Tyk Nodes it i onfigured to monitor on a host. This means that the host manager, alongside tyk, can be used to orchestrate changes to your API gateway infrastructure concurrently.
