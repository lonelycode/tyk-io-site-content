+++
draft = false
title = "Tyk Overview"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_8]
    weight = -300
    
+++

Tyk consists of three components, the Tyk API Gateway, the Tyk Dashboard and the Tyk Host Manager. 

The Tyk API Gateway
-------------------

This is the core component of Tyk and can be used independently of the others, it is a reverse proxy that can manage your
API traffic and act as a full-featured Gateway to your upstream systems. 

The gateway has it's own RESTful API, which you can use to manage API Definitions, Keys, the Tyk cluster and usage data.

The Tyk Dashboard
-----------------

This is the GUI-orientated part of Tyk, and offers analytics data, user management, portal management, developer mangement and a raft
of features focussed around using Tyk in a multi-team, multi-server, integrated environment.

The Tyk Dashboard has it's own Advanced API, unlike the Gateway API, access is limited by Organisation and user, more rigid checks are in 
place for data integrity and more security is applied to what actions are propagated to your Tyk Nodes. This API can do everything that 
the gateway APi can do, but offers a better integration point for internal teams and systems.

The Dashboard also acts as the server for the Tyk API Portal, each organisation configured in the dashboard has it's own
portal, and can publish it's APIs to that portal to enable the documentation and on-boarding workflow.

The Tyk Host Manager
--------------------

The host manager acts as a bridge between the Tyk gateway and your webserver, it is specialised on NGinX and can do two things:

1. It can generate site definitions for NGinX to point to your upstream API (so your subdomain my-api.example.com will point to an internal upstream tyk installation)
2. It can manage a Tyk installation by hot-reloading Tyk when there is a change registered by the Dashboard, although this use is now deprecated since nodes can hot-reload themselves.

The host manager is not required, it is a utility aplication that can make large, multi-team API deployments dynamic with little to no 
web server management.

The host manager is bundled with the Tyk Dashboard tarball.

