+++
draft = false
title = "Settings"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -290
+++

The Tyk portal is highly configurable to enable different workflows and API publication scenarios.

The first place to start with the Tyk Portal is the settings screen, here you can set up
the main options that will determine key properties of how your portal will behave.

### Core Settings

The main settings in this screen determine how the portal will handle new developers
and enrollment.

#### Disable developer signup

If you check this option, developers will not be able to sign up to your portal, this blocks
any new developers from signing up at all, existing developers will be able to log in if the
login is enabled.

#### Disable developer login

By default, developers can signup and login to your portal, if you enable this option, they will
no longer be allowed to log in (if they visit the page, it will show a disabled message).

#### Require key approval

By default Tyk will automatically generate a key for your developer for APIs that have been published
to the API catalogue. If you would rather the developer waits for you to approve the key, then enable
this option. 

You can find all key rewuests in the "Key requests" section of the dashboard.

#### Redirect key requests

If you wish to have a custom signup flow after a key request is made (e.g. a payment flow or another 
enrollment form with a different system, enable this option and set the appropriate redirect URL.

### Signup form customisation

If you wish develoeprs to submit additional details as part of their signup form, you can register the 
field names here, then add them to the form in the sign-up template (they will not be validated, we leave
this up to you) and they will be stored in the database alongside the user profile.

These fields will also be exposed in the developer profiles secion in the dashboard so you can view them
at any time.

### Key request form customisation

Similarly to the signup form customisation, you can set the field names to store as part of a key request,
e.g. "Why do you want access to this API?" etc.).

Again these keys will need ot be maually exposed as form fields in the template for the processor to store them
correctly.

### Customised email

It is not possible to customise the email copy on a per-organisation basis using the fields available in this section. The "From Name", "From Email", and body copy for both main emails that are sent by the dashboard can be customised.

