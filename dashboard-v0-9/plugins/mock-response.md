+++
draft = false
title = "Mock responses"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Sometimes, when creating a new API, or when making a development API available to an external team, it can help to be able to mock out responses for an API endpoint. Tyk makes this possible by selecting the "Mock Response" options from the plugins drop down.

**Note:** In order for mocks to be enabled, the path must *also* be in a list, we recommend adding the path to a white list. If this isn't done, then the mock will not be saved on update.

**API Blueprint:** If you have imported an API Blueprint definition, and selected the mocks option in the importer, then your whole API will be a white list.

The options are pretty clear for a mock:

- **Code:** the status code to respond with
- **Response body:** The response body
- **Headers:** The headers to inject with the response

