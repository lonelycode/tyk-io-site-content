+++
draft = false
title = "Notifications and events"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -280
    parent="events"
+++

Tyk Dashbaord and the portal no support email notifications powered by Mandrill (though other back-ends can be developed), mandrill is a free email API that handles emails, tempaltes and list subscription for you, it was chosen for it's ease of setup vs. a traditional SMTP server.

In order to enable email suuport in the portal and dashboard, you will need to add the configuration block to your tyk_analytics.conf file:

	"email_backend": {
        "enable_email_notifications": true,
        "code": "mandrill",
        "settings": {
            "ClientKey": "YOUR-KEY"
        },
        "default_from_email": "support@yourdomain.com",
        "default_from_name": "Foo at Bar.com"
    },

You can sign up for a mandirll account [here](https://www.mandrill.com/signup/).

## How email notifications work.

If you have enabled email notifications, the portal will attempt to send notifcations regarding a users sign-up status or key request status to their username email-address. these tempaltes can be found in the `portal/email_templates` folder.

the tempaltes are twofold, text-only and html, please see the standard included ones to see the various template fields taht can be customised.

## Events 

The dashboard and portal now also support a certain level of events that you can use to notify your systems of various things that have happened in the portal.

To configure them, add an `event_options` section to an Organisation document when you are creating them (see the Advanced Management API documentation to see how to create an Organisation record using the API).

Within this object, you can then register either webhooks or an email address to notify when an event occurs:
	
	event_options: {
		api_event: {
	      webhook: "http://posttestserver.com/post.php?dir=tyk-events",
	      email: "test@test.com"
	    },
	    key_event: {
	      webhook: "http://posttestserver.com/post.php?dir=tyk-key-events",
	      email: "test@test.com"
	    },
	    key_request_event: {
	      webhook: "http://posttestserver.com/post.php?dir=tyk-key-events",
	      email: "test@test.com"
	    }
	}

The following events are supported:

### `api_event`

When an API is Created, updated or deleted

### `key_event` 

When a key is Created, updated or deleted.

### `key_request_event`

When a portal key request is created or updated.

