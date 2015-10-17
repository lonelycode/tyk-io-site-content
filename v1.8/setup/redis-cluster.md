+++
draft = false
title = "Redis Cluster"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
    weight = -110
+++

Tyk now supports redis cluster integration. To work with a redis cluster, simply specify it in your tyk.conf file:

	"storage": {
        "type": "redis",
        "enable_cluster": true,
        "hosts" : {
            "server1": "6379",
            "server2": "6380",
            "server23: "6381",
        },
        "username": "",
        "password": "",
        "database": 0,
        "optimisation_max_idle": 100
    },

### Tyk Dashboard

Tyk Dashboard also supports redis cluster, simply update the tyk_analytics.conf to use the cluster like so:

	"redis_hosts": {
        "server1": "6379",
        "server2": "6380",
        "server23: "6381",
    },
    "enable_cluster": true

### Tyk Host Manager

Tyk host manager can also conect to the redis cluster, update the host_manager.json file with the hosts entry, inthe same way as the tyk dashboard:

	"redis_hosts": {
        "server1": "6379",
        "server2": "6380",
        "server23: "6381",
    },
    "enable_cluster": true


