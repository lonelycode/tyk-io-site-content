+++
draft = false
title = "Working with NGinX"
date = 2014-07-29T10:59:33Z
[menu.sidebar_v1_9]
    parent = "configuration"
+++

**As of version 1.9 Tyk no longer requires NginX and the Tyk Host Manager to work** However, the Tyk host manager is still bundled with Tyk Dashboard and Tyk will continue to work with the host manager, but requires manual installation and configuration.

An example configuration is shown below:

`nginx.conf`:

    worker_processes 1;

    error_log /var/log/nginx/error.log;
    pid /var/run/nginx.pid;

    events {
        worker_connections 1024;
        use epoll;
    }

    http {
        # Enumerate all the Tyk servers here
        upstream tyk {
            server 127.0.0.1:5000, 127.0.0.1:5001, 127.0.0.1:5002;
        }

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        access_log /var/log/nginx/access.log;

        keepalive_timeout 65;
        proxy_read_timeout 200;
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        gzip on;
        gzip_min_length 1000;
        gzip_proxied any;
        gzip_types text/plain text/css text/xml
                   application/x-javascript application/xml
                   application/atom+xml text/javascript;

        proxy_next_upstream error;

        include /etc/nginx/sites-enabled/* ;
    }

The only difference to a standard NGinX setup is the upstream `tyk section`, here we enumerate a list of running Tyk
instances, Tyk can be started on a different port by simply using the `--port` flag.

You can then use the `sites-enabled` folder to set up your API / Gateway server:

    server {
        listen 80;
        server_name gateway.myserver.com;

        gzip              on;
        gzip_buffers      16 8k;
        gzip_comp_level   4;
        gzip_http_version 1.1;
        gzip_min_length   1280;
        gzip_types        text/plain text/css application/x-javascript text/xml application/xml application/xml+rss text/javascript image/x-icon image/bmp;
        gzip_vary         on;

        # The Go application server
        location / {
            rewrite /(.*) /API_LISTEN_PATH/$1 break;

            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://tyk;
        }
    }

The above configuration will accept requests to `gateway.myserver.com`, then rewrite any of the requests coming in to
the `listen_path` set up for this specific api in it's API Definition, and forward the request upstream to your load-balanced
Tyk instances.

This should ensure that you can present a clean API front-end to your users, without having to worry about API ID's, listen paths or
ugly gateway URL's, if you wanted to, you could plumb Tyk into your existing architecture without changing your current endpoints
(assuming you are using NGinX, of course).
