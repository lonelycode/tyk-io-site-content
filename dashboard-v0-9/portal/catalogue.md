+++
draft = false
title = "API Catalogue"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -280
+++

The API Catalogue is the central place for you to manage which APIs you are giving developers registered
to your portal the ability to enroll for. 

The API catalogue is completely seperate from the regular API list section of the dashboard, as you may only witsh to 
expose some of those APIs managed by Tyk to end users.

### API Policy

You can set which API policy (see the policies section for more detail) to apply to a developer key when they 
enroll for access. Each developers key will be given this policy ID when the key is generated. The policy does
not have to only give access to one API, it can give access to multiple API's if you wish (since it is an
access control object)

### Documentation

The Tyk Portal supports the publication of both Swagger and API Blueprint documentation, you can also use the 
basic CMS features to create your own documentation pages in markdown.

#### API Blueprint

Importing API blueprint documents means having an important dependency installed: 
[Aglio - the API Blueprint Renderer](https://github.com/danielgtaylor/aglio), as we use this application to
handle the AST translation into the static content pages.

You can find the templates for bluerint documentation under the `portal/jade/` folder and in `portal/templates/` folder

#### Swagger

Importing a swagger document is easy, we use the swagger-ui JS renderer with our own template to upublish this documentation.

You can find the templates for swagger documentation in the `portal/templates/swagger.html` file

