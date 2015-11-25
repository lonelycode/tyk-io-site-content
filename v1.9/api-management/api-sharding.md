+++
draft = false
title = "API Sharding and Tagging"
date = 2014-07-29T10:56:22Z
[menu.sidebar_v1_9]
    parent = "management"
    weight = -100
+++

It is possible to use tags in various Tyk objects to change the behaviour of a Tyk cluster or to modify the data that is sent to the analytics engine. Tags are free-form strings that can be embedded in Node configurations, API definitions, Policies and individual Keys.

Tags are used in two ways: To segment a cluster into various "zones" of API management, and secondly, to push more data into the analytics records to make reporting and tracking easier.

## API Sharding

API Sharding is what we are calling our approach to segmenting a Tyk cluster (or data centers) into different zones. An example of this in action would be to imagine you have separate VPCs that deal with different classes of services, lets say: Health, Banking and Pharma.

You don't need the nodes that handle all the traffic for your Pharma APIs to load up the definitions for the other zones' services, this could allow someone to send unexpected traffic through (it may not go anywhere).

Alternatively, you could use segmentation to have separate API definitions for multiple data centers. In this way you could shard your API definitions across those DC's and not sorry about having to reconfigure them if there is a failover event.

Sharding APIs is very easy, all you do is tell the node in the `tyk.conf` file what tags to respect:

	```
	...
	"db_app_conf_options": {
	        "node_is_segmented": true,
	        "tags": ["health", "finance"]
	},
	...
	```

Tags are always treated as OR conditions, so this node will pick up all APIs that are marked as "health" or "finance".

In your API definition, simply add a `tags` section:

	```
	"tags": ["health"]
	```

You will need to restart the node in order for the changes to take effect.

## Analytics and Reporting

In order to use tags in analytics, there are two places where you can add a `"tags":[]` section: a Policy Definition, and a Session object for a token.

Policy tags completely replace key tags, these tags are then fed into the analytics system and can be filtered in the dashboard.

### Node tags

If your API is segmented, node tags will be appended to the analytics data, this will allow you to filter out all traffic going through a specific node or node cluster.
