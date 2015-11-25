+++
draft = false
title = "SSL"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -100
+++

As of version 1.8.3.1 Tyk supports SSL connections. To enable SSL in your Tyk node, you will need to modify the tyk.conf file to include a server options section like so:

	```
	"http_server_options": {
        "use_ssl": true,
        "server_name": "banana.com",
        "min_version": "1.2",
        "certificates": [
            {
                "domain_name": "*.banana.com",
                "cert_file": "new.cert.cert",
                "key_file": "new.cert.key"
            }
        ]
    },
    ```
You can enter multiple certificates, that link to multiple domain names, this enables you to have multiple SSL certs for your gateways if they are providing access to different domains via the same IP.

The `min_version` setting is optional, you can set it to `0`, `1` or `2` to to have Tyk only accept connections from TLS V1.0, 1.1 and 1.2 respectively.
