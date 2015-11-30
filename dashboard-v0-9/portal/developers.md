+++
draft = false
title = "Developers"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -250
+++

Users that are signed up to your portal as called "Developers" in Tyk parlance, these users have access to a
dashboard page which show them their API usage over the past 7 days as well as the policy and quota limits
on their relevant keys.

Developers can sign up to multiple APIs using the APi catalogue, only logged in users are allowed to access 
this section of the site.

Developer accounts belong to an organisation ID, so accounts cannot be shared across organisations in a Tyk
Dashboard setup.

### Customising user signup

As mentioned in the portal settings section, the user signup form can have additional custom fields added (not uploads).
These fields as in the key request form, should start with an uppercase letter, they must also be manually exposed
as form fields in the `portal/templates/signup.html' template.
