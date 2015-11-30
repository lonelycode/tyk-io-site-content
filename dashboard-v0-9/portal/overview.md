+++
draft = false
title = "Portal Overview"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -300
+++

The Tyk portal is a new feature that enables you to automate developer management, API enrollment, API documentation and rudimentary CMS
functionality.

The role of the portal is simple: to make it easy for you to generate a portal and expose your APIs and their documentation via Tyk 
with minimal effort.

The portal has the following key features:

1. API Catalogue - list the available APIs that you have exposed
2. API Documentation - The portal supports manual, Swagger and API Blueprint imports and templates to expose your API documentation
3. Developer signup - Allow developers to sign up and log into the portal with customisable profiles
4. API Enrollment and Key Requests - Allow developers to apply for API access and control whether keys should be auto-assigned or require approval
5. Token Policies - Set policies for each API so when a user enrolls they are given a uniform access policy that controls their level of access
6. Developer Dashboard - Where your developers can monitor their API usage and view their policy levels

All templates managed by the portal use twitter bootstrap, so they are mobile enabled by design and are easy to theme and modify for
your brand. 

### Portal workflow

By default, the portal workflow for a developer is as follows:

1. Developer visits portal
2. Developer uses the registration form to register for the portal
3. Developer logs in with their credentials
4. Developer views the API catalogue
5. Developer enrolls for access
6. Developer fills out a key request form
7. Developer is given an API key

For the API owner, the workflow is slightly different:

1. Create an API definition in the portal and enable it
2. Set up a key policy for enrollment
3. Publish the API to the API catalogue
4. Upload the relevant documentation format
5. Select whether keys need to be manually processed or should be automatically created

Setting these base options will give you your portal.

