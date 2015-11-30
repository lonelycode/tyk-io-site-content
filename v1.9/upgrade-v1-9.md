+++
draft = false
title = "Upgrading to v1.9"
date = 2015-08-01T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_9]
    weight = -290
home = "1"
+++

If you are upgrading from a previous version of Tyk, there's a few things to take not of when making the upgrade, in particular there are some settings in the configuration files that will need to be enabled in order for backwards compatibility to remain on certain features.

Backwards compatibility affects the dashboard more than the gateway.

### Tyk Gateway

The Tyk gateway will, as of v1.9, disable the JavaScript sandbox that runs all custom middleware and event handlers for APIs. To ensure continuous running when upgrading, add the following to your tyk.conf:

	"enable_jsvm": true

### Tyk Dashboard

In previous versions of Tyk dashboard, the hostname that was used to help the gateway construct friendly URL's for telling users their API addresses and for generating the URL in the swagger documentation publisher was handled by a single `hostname` attribute. This has been moved and renamed.

In order to retain the same functionality, add the following section to your tyk_analytics.conf:

	"host_config" : {
        "override_hostname": "YOUR OLD HOSTNAME VALUE",
        "portal_root_path": "/portal"
    }

### Installation locations

If installing using the RPM or YUM repositories, Tyk will be installed into /opt/tyk-* including all configuration files. Tyk will continue to look for it's default config in /etc/tyk.conf, but all the init scripts will specify to use the configuration stored in the /opt/tyk-* directories.

