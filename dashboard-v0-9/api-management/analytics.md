+++
draft = false
title = "Analytics Data"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent="api-management"
    weight = -150
+++

Analytics data is the core use for the dashboard, and multiple views on how your upstream APIs are being used are made available. Each analytics chart is a composite of three metrics:

- Upstream latency (blue)
- Successful hits (green)
- Errors (orange) - Error values are when Tyk has registered an error, not the upstream API

It is possible to disable these charts by selecting the coloured dot in the legend.

### The Main Dashboard

In the main dashboard you will see a global view of your API statistics:

- A global Traffic data
- An error breakdown pie chart
- The top endpoints in use

### Filtering Data

Using the drop downs above the graph will set your filter for the entire dashboard (all views), this means if you set the start and end dates, then press the search button, your view will refresh throughout the site to focus only on the data that you are interested in.

You can filter your analytics views by:

- Start date
- End date
- API version
- Granularity (Monthly, daily or Hourly)

**Note:** API Versions are generated from the data available to Tyk, so it will show a list of all API and version options available to it.

### Tags

If you are using the tagging features in your Tyk nodes or in your POlicies / Keys, then you can use the tag field to filter out traffic by the tag that is associated. Tags are processed as OR'ed parameters, so they are aways inclusive of one another, not exclusive.

### Focusing your data view

There are three other key views on your data: By Key, By endpoint and Errors. These allow you to drill down to view how different users are using your API, which are the most popular users and which endpoint see the most traffic.

All views will return the same data view options (latency, errors, success) except for the errors view, which is focused on displaying only error reports and all views will filter by tags.

