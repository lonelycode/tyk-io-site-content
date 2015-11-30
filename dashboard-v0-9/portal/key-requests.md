+++
draft = false
title = "Key Requests"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -260
+++

When a user enrolls for API access, one of two things will happen:

1. The user will be approved and given an API key
2. The user will be put into a holding queue for an administrator to manually approve their key

### Key request form

As mentioned in the settings section, it is possible to customise the fields that are exposed to the sign up form and 
stored alongside the key request. These custom fields can be very useful for getting implementation details and other
relevant CRM information.

Keys should always start with an uppercase letter, and should be a single word, ideally in camel-case. The values
of these keys will be displayed in the key request.

To capture these details as a form parameter, edit the form in the `portal/templates/request_key.html` page.

### Automated Keys

If this APi key is from a hashed Tyk install, it will be the **only** time they can see it. This is important,
as otherwise the key will need to be recreated by removing it from the developer profile and generating a new key request.

### Key approval

If key approval is enabled, an admin will need to use the key requests section to review and authorise a key request. 
Once a key request is authorised, the relevant API key is generated. This API key is **not** sent to the user, and in
the case of a hashed API configuration, will be the *only* time the key will be visible in plaintext.

It is left to the administrator to transmit the key securely to the developer.