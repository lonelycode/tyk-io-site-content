+++
draft = false
title = "Header Transforms"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Sometimes you will need to inject a header into your inbound request (for example, a security token that your app understands), or remove headers that could be damaging.

Alternatively you may want to inject headers into an outbound response from your system because you don't want to modify the underlying service to do so, or to enrich the outbound request with additional headers that you require for the response type.

To enable header modification, select the "Modify Headers" option from the drop down box in the endpoint designer.

To use this feature - first select which type of transform you want (request or response), and then fill out the form.

- Delete headers: headers in this list will be removed
- Add headers: These header and values will be added

**Note**: Headers are removed first, and then added, this mean that header substitution is possible by removing the same header that is being added.