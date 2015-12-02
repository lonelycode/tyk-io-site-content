+++
draft = false
title = "Sending Emails"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "configuration"
+++

Tyk Dashboard supports sending emails using [Mandrill](https://mandrillapp.com) (more back-ends to come), to get email set up for your installation, add the following to your `tyk_analytics.conf` file:

	```
	"email_backend": {
        "enable_email_notifications": true,
        "code": "mandrill",
        "settings": {
            "ClientKey": "xxxxxxxxx"
        },
        "default_from_email": "bob@domain.com",
        "default_from_name": "The Dude"
    }
    ```

The email templates for the portal and system messages are located in the `portal/email_templates` folder.