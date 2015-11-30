+++
draft = false
title = "Uptime tests"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "uptime"
    weight = -600
+++

As of v1.9 Tyk supports a kind of built-in "uptime awareness" of the underlying services and hosts that it is managing traffic for by actively polling user-defined endpoints at set intervals.

Tyk uptime awareness is not meant to replace traditional uptime monitoring tools, in fact, it is designed to supplement them by offering a way to bypass unhealthy nodes when they are down as art of Tyk's role as an API Gateway. 

### How do the uptime tests work?

When uptime tests are added into a Tyk cluster, a single node will elect itself as master, masters stay in this state using a dead-mans-switch, by keeping a key active in Redis, masters are re-elected or confirmed every few seconds, if one node stops or fails, another can detect the failure an elect itself master.

The master node will then run the uptime tests allocated to the cluster (shard group).

The node running the uptime test will have a worker pool defined so that it can execute tests simultaneously every few seconds determined by a node-configurable interval loop. Depending on how many uptime tests are being run, this worker pool should be increased or decreased as needed.

### Configuring uptime tests in Tyk

To configure uptime tests, add the relevant section to your `tyk.conf`:

	```
	"uptime_tests": {
        "disable": false, // disable uptime tests on the node completely
        "config": {
            "enable_uptime_analytics": true,
            "failure_trigger_sample_size": 3,
            "time_wait": 300,
            "checker_pool_size": 50
        }
    }

**What does the above configuration do?**

- `"disable": false` - This tells Tyk to run uptime tests, if you do not want any uptime tests to run on a node, select "true" and they will be disabled on those nodes (this could be useful if you are running uptime tests in a separate group of Tyk instances)
- `"enable_uptime_analytics": true` - Tyk supports the recording of the data that is generated by the uptime tests, these can then be tabulated in the dashboard.  Since uptime tests run constantly, they can generate large amounts of data, for some users who do not wish to manage this data, they can disable it by setting this value to `false`
- `"failure_trigger_sample_size": 3` - Here we tell Tyk to trigger a `HostDown` or HostUp` event after the tests has failed or passed at least three times (this can protect against false positives, but can increase lead incident time due to the verification)
- `"time_wait": 300` - Run the tests every 300 seconds
- `"checker_pool_size": 50` The worker pool for uptime tests, here we say that yk should keep 50 idle workers in memory to send tests to, in other words, with this configuration, you are pretty much guaranteed asynchronous testing for up to 50 tests.

### Creating an Uptime test

Uptime test check-lists usually sit within API configurations, so in your API Definition add a section for the tests:

	```
	uptime_tests: {
	    check_list: [
	      {
	        "url": "http://google.com/"
	      },
	      {
	        "url": "http://posttestserver.com/post.php?dir=uptime-checker",
	        "method": "POST",
	        "headers": {
	          "this": "that",
	          "more": "beans"
	        },
	        "body": "VEhJUyBJUyBBIEJPRFkgT0JKRUNUIFRFWFQNCg0KTW9yZSBzdHVmZiBoZXJl"
	      }
	    ]
	},
	```

Uptime tests are not versioned.

In the above example there are two forms for the Uptime test, a "quick" form, which assumes a GET request:

	{
		"url": "http://google.com/"
	}

Or a long form, which allows for a full request to be checked or mocked:

	{
        "url": "http://posttestserver.com/post.php?dir=tyk-checker-target-test&beep=boop",
        "method": "POST",
        "headers": {
          "this": "that",
          "more": "beans"
        },
        "body": "VEhJUyBJUyBBIEJPRFkgT0JKRUNUIFRFWFQNCg0KTW9yZSBzdHVmZiBoZXJl"
      }

The body is `base64` encoded 

**NOTE:** using the simplified form will not enforce a timeout, while the more verbose form will fail with a 500ms timeout.

### Downtime detection and service availability

If you have configured Tyk to use round-robin load balancing, you can enable an option in the `proxy` section of your API Definition that will check the hostname of the outbound Tyk request (to your service) against the downtime list to see if the server is active, it the host is marked as "down" Tyk will skip to the next host in it's list before making the request:

	```
	...
	"proxy": {
		...
		"check_host_against_uptime_tests": true,
		...
	}
	...
	```

### Uptime and downtime events

When Tyk detects downtime on a test, it will trigger a system event called `HostDown`, when that host returns to service, it will trigger `HostUp`. These events are triggered in the same event system as other Tyk Events, and therefore can have any kind of action performed:

- A logger action
- A web hook to a notification URL
- A custom JS script for complex API interactions

Please see the section on the [event system](../event-handlers/event-handlers/) on how to add event handlers to your API Definition.

Since tests are on a URL-by-URL basis, you could potentially see multiple host down events for a single host where multiple endpoints are being tested.






