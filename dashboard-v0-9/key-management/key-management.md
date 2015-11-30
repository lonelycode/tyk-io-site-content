+++
draft = false
title = "Key Management"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -270
    parent="key-management"
+++

API Keys can be generated using the dashboard interface, the options that are available here mirror the options that can be set by creating a session object manually for a key via the Core Tyk REST API.

![Key Manager - settings](/imgs/key-settings.png)

The key management view enables you to set various options around the key being created. 

**Warning**: It is only possible to create Keys for APIs that have been loaded by the Tyk nodes

### Enforce HMAC signing

If you have enabled the HMAC middleware on an API configuration, you will need to set the keys up to force HMAC signing, please see the documentation on HMAC signing in the Tyk Core documentation. This will only work with new keys, as an HMAC secret must be generated for the end user.

### Basic Authentication

If you are creating a new Basic Auth User, the key needs to be designated as such, selecting this option will let you set the username and password for the user. usernames must be unique an usernames only exist within the context of the organisation that owns the API.

*Note:* In the Key List, and when editing a basic auth user, the key will appear with a prefixed UUID, this is the organisation ID, this is *required* by Tyk to find the password information and validate the user when a request is inbound, but does not affect the actual username that the user uses to authenticate.

### Rate Limiting

Set the user's rate limits here, requests / per period (in seconds)

### Expires

If the key is meant to expire (recommended), set the value here. The dashboard only allows pre-set values to be assigned to a key, using the API any value can be applied.

### Usage Quotas

Set the quote to `-1` for an unlimited quota, otherwise set a value and a reset period. Again the reset period is fixed using the dashboard, however a custom value can be set using the API.

### Access Rights

This section will show which APIs and versions the key has access to, simply select the AI an version from the drop-down list and click "Add". **Note:** If no access rights are added, then the key will have access to ALL APIs in the system.

### Tags

A Tyk PI key can be given tags that are injected into Analytics data when requests pass through a Tyk node. Add tags to the key here to have them used by the Tyk node. Tags are active immediately.

**Note:** If a key is attached to a policy, then policy-level tags override the key tags.

### Policy

In many cases it is preferable to have token rules managed in bulk (it saves the API owner updating thousands of keys if they wish to change quota rules). You can select a policy from this drop-down to have the key start using the quota and throttling rules of a policy instead of those specified int he token itself.

