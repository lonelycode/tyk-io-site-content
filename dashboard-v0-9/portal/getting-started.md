+++
draft = false
title = "Getting started"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -440
+++

Getting started with the portal requires only one thing: a working sub-domain

The default Tyk portal templates expect to be access from a URL that looks like this: `somedomain.com/portal`, this is 
because the root URL is reserved for the dashboard login. this isn't ideal, and may be something we change in a later
version.

However, given this requirement, we have included a sample portal nginx configuration with the host manager which shows 
which directories need to be exposed and how URL rewrites should be enforced, here is the site configuration te plate:

	server {
        server_name yoursub.domain.com;

        gzip              on;
        gzip_buffers      16 8k;
        gzip_comp_level   4;
        gzip_http_version 1.1;
        gzip_min_length   1280;
        gzip_types        text/plain text/css application/x-javascript text/xml application/xml application/xml+rss text/javascript image/x-icon image/bmp;
        gzip_vary         on;

        # Portal redirect by OrgID
        location /portal/ {
            rewrite /portal/(.*) /{{.OrgId}}/portal/$1 break;

            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://dashboard;
        }
        location /portal-assets/ {
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://dashboard;
        }
        location /api/public {
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://dashboard;
        }
	}

As you can see from above, we are rewriting `/portal` to the overlong and complex `/{{.OrgId}}/portal/`, this is because
all portals from a dashboard instance are exposed as a sub-directory for each organisation, basically you get one portal per org.

The upstream proxy looks like this:

	upstream dashboard {
        server 127.0.0.1:3000;
    }

The configuration at the top of this document means that the root login is actually not available (and can be blocked), and so 
can only be accessed via the direct URL and port.

The easiest way to get this started, if you are using our vagrant setup guide, is to modify your localhost file to create
a fake domain to point at the vagrant instance, and then configure NginX on the vagrant box appropriately.

Once you have set up the sub-domain and tyk and the dashboard are both running, you should be able to navigate to the URL you've created and see the default home screen layout: