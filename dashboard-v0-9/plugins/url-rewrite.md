+++
draft = false
title = "URL Rewriting"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Tyk offers a simple form of URL rewriting, to enable the URL rewrite, simply select "URL Rewrite" from the drop-down menu in the plugins column of the Endpoint Designer.

URLRewriting can be very useful when you want to present a "standard" external interface for your API that translates to your own scheme in your back-end, which may not be using resource-schemes.

![Plugins - URL Rewrite](/imgs/url-rewrite.png)

It is important to note that rewrites are two-step, they are matched against the initial path, and unpacked using the "match pattern". Tyk will match the `path` element, like any other path in the endpoint designer, here you can use wildcards such as `widgets/{id}` to show that anything should match.

However, it is important to remember that wildcards like this `{id}` actaully get converted to `(.*)` regexes, and are not extracted, they are thrown away and are ONLY used for mathing the apth to trigger the middleware.

The transform is handled by the other two options, whih can use any valid regex to group paramaters, these are then converted into `$index` tokens which can be used in your rewrite as in the example above.