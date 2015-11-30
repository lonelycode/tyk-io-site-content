+++
draft = false
title = "Security Overview"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -305
    parent="security"
+++

The dashboard (and Advanced API) provide a more structured security layer to managing Tyk nodes. This is explained in detail under the API documentation but will be covered in brief here.

### Organisations, APIs and Users

With the Advanced API and a database-backed Tyk setup, (and to an extent with file-based API setups - if diligence is used in naming an creating definitions), the following security model is applied to the management of Upstream APIs:

- **Organisations**: All API's are *owned* by an organisation, this is designated by the OrgID parameter in the API Definition
    - **Users**: All users created in the dashboard belong to an organisation (unless an exception is made for super-administrative access)
    - **APIs**: All APIs belong to an Organisation and only Users that belong to that organisation can see the analytics for those APIs and manage their configurations
    - **API Keys**: API Keys are designated by organisation, this means an API key that has full access rights will not be allowed to access the APIs of another organisation on the same system, but can have full access of all APIs within the organisation
        - **Access Rights**: Access rights are stored with the key, this enable s akey to give access to multiple APIs, this is defined by the session object in the core Tyk API.
        
Most of this complexity is invisible to the dashboard user as they will by default only ever see the security scope of their organisation.

### Creating Organisations and Users

The Tyk management APi application (`tyk_analytics`), can use a command line flag to create users and organisations (this is used in the initial setup of a node):

    #~/> ./tyk_analytics --neworg --newuser 

The `--neworg` flag creates a new organisation, and the `--newuser` flag creates a new user, which can be assigned to an organisation. Organisations and un-tagged users can also be created using the advanced API, this is covered elsewhere.

### Special cases

The only special case is when a user is created without an empty OrgID, this can be done via the command line by not selecting an API on user creation, or via the advanced API.

Users that are not assigned to an Organisation gain special privileges in the dashboard, and can see all APIs, users and all analytics data disregarding organisations. These users will not be able to add new users to other organisations though, only to their own content - o any users they crate will also be super-users.
