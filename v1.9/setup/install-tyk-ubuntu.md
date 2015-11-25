+++
draft = false
title = "Install Tyk on Ubuntu"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -300
+++

Tyk has it's own APT repositories hosted by the kind folks at [packagecloud.io](https://packagecloud.io), which makes it easy, safe and secure to install a trusted distribution of the Tyk Gateway stack.

This tutorial will run on an [Amazon AWS](http://aws.amazon.com) *Ubuntu Server 14.04 LTS* instance. We will install both the Tyk Gateway and the Tyk Dashboard with all dependencies stored locally.

We're installing on a `t2.micro` because this is a demo, you'll need more RAM and more cores for better performance.

**Pre-requisites**:

- Ensure port `8080` is open: this is used in this guide for Gateway traffic (API traffic to be proxied)
- Ensure port `3000` is open: This is used by the dashboard to provide the GUI and the Developer Portal

## Installation steps:

### 1. Set up our APT repositories:

First, add our GPGP key which signs our binaries:

	curl https://packagecloud.io/gpg.key | sudo apt-key add -

Do the same for Mongo (this may change, correct at time of writing):

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10

Run update:

	sudo apt-get update

Since our repositories are installed via HTTPS, you will need to make sure APT supports this: 

	sudo apt-get install -y apt-transport-https

Now lets add the required repos and update again (notice the `-a` flag in the second Tyk commands - this is important!):

	echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list

	echo "deb https://packagecloud.io/tyk/tyk-gateway/ubuntu/ trusty main" | sudo tee /etc/apt/sources.list.d/tyk_tyk-gateway.list

	echo "deb-src https://packagecloud.io/tyk/tyk-gateway/ubuntu/ trusty main" | sudo tee -a /etc/apt/sources.list.d/tyk_tyk-gateway.list

	echo "deb https://packagecloud.io/tyk/tyk-dashboard/ubuntu/ trusty main" | sudo tee /etc/apt/sources.list.d/tyk_tyk-dashboard.list

	echo "deb-src https://packagecloud.io/tyk/tyk-dashboard/ubuntu/ trusty main" | sudo tee -a /etc/apt/sources.list.d/tyk_tyk-dashboard.list

	sudo apt-get update

**What we've done here is:**

- Added the Mongo DB repository for the altest version (3.0 as of time of writing)
- Added the Tyk Gateway and Tyk Dashboard repositories
- Updated our package list

### 2. Install the Tyk stack:

We're now ready to install Tyk Gateway and tyk Dashboard, along with all the main dependencies: Redis and MongoDB. To install everything run:

	sudo apt-get install -y mongodb-org redis-server tyk-gateway tyk-dashboard

What we've done here is instructed apt-get to install MongoDB, Redis, the Tyk Gateway and the Tyk Dashboard without prompting, wait for the downloads to complete.

When Tyk is finished installing, it will have installed some init scripts, but it will not be running yet. The next step will be to setup each application - thankfully this can be done with two very simple commands.

### 3. Configure Tyk Gateway

You can set up the core settings for Tyk Gateway with a single setup script, however for more involved deployments, you will want to provide your own configuration file, to get things running lets run:

	sudo /opt/tyk-gateway/install/setup.sh --dashboard=1 --listenport=8080 --redishost=localhost --redisport=6379 --domain="" --mongo=mongodb://localhost/tyk_analytics

What we've done here is told the setup script that:

- `--dashboard=1`: We want to use the dashboard, so to enable all the flags for using MongoDB and Analytics tracking
- `--listenport=8080` Tyk should listen on port 8080 for API traffic
- `--redishost=localhost` Use redis on the hostname: localhost
- `--redisport=6379` Use the default redis port
- `--domain=""` Do not set a domain for the gateway, see the note on domains below for more about this
- `--mongo=mongodb://localhost/tyk_analytics` Use this connection string to connect to the local MongoDB instance and use the `tyk_analytics` database

#### Pro Tip: Domains with Tyk Gateway

Tyk Gateway has full built in domain support, you can:

- Set Tyk to listen only on a specific domain for all API traffic
- Set an API to listen on a specific domain (e.g. api1.com, api2.com)
- Split APIs over a domain using a path (e.g. api.com/api1, api.com/api2, moreapis.com/api1, moreapis.com/api2 etc.)
- If you have set a hostname for the gateway, then all non-domain-bound APIs will be on this hostname + the `listen_path`

In this example, we don't want Tyk to listen on a single domain, and we can always set up custom domains at the API level in the dashboard. It is recommended to leave the Tyk gateway domain unbounded for flexibility and ease of deployment.

### 4. Configure Tyk Dashboard

We can set the dashboard up with a similar setup command, the below will get the dashboard set up for the local instance, **make sure to use the actual DNS hostname or the public IP of your instance as the last parameter**:

	sudo /opt/tyk-dashboard/install/setup.sh --listenport=3000 --redishost=localhost --redisport=6379 --mongo=mongodb://localhost/tyk_analytics --tyk_api_hostname=$HOSTNAME --tyk_node_hostname=http://localhost --tyk_node_port=8080 --portal_root=/portal --domain="XXX.XXX.XXX.XXX"

What we have done here is:

- `--listenport=3000` Told Tyk Dashboard (and Portal) to listen on port 3000
- `--redishost=localhost` Tyk Dashboard should use the local redis instance
- `--redisport=6379` Tyk Dashboard should use the default port
- `--domain="XXX.XXX.XXX.XXX"` Bind the dashboard to the IP or DNS hostname of this instance (required)
- `--mongo=mongodb://localhost/tyk_analytics` Use the local MongoDB (should always be the same as the gateway)
- `--tyk_api_hostname=$HOSTNAME` - Tyk Dashboard has no idea what hostname has been given to Tyk, so we need to tell it, in this instance we are just using the local HOSTNAME env variable, but you could set this to the public-hostname/IP of the instance
- `--tyk_node_hostname=http://localhost` - Tyk Dashboard needs to see a Tyk node in order to create new tokens, so we need to tell it where we can find one, in this case, use the one installed locally
- `--tyk_node_port=8080` Tell the dashboard that the Tyk node it should communicate with is on port 8080
- `--portal_root=/` - We want the portal to be shown on the root of whichever domain we set for the portal

5. Start Tyk and Tyk dashboard:

		sudo service tyk-gateway start
		sudo service tyk-dashboard start

6. Bootstrap the dashboard with an initial user and organisation:

When Tyk Dashboard is created for the first time, it has no initial user base or organisation to add data to, so we need to add this.

The best way to add this data is with the Admin API, to make it really easy we've supplied a bootstrap script that will set you up. If you want to customise it, take a look at the file in `/opt/tyk-dashboard/install/bootstrap.sh`

**To bootstrap your instance**:

	sudo /opt/tyk-dashboard/install/bootstrap.sh XXX.XXX.XXX.XXX

This command tells the bootstrap script to use the localhost as the base for the API calls, you can run the bootstrap remotely and change the first command line parameter to the DNS hostname of your instance.

You will now be able to log into and test your Tyk instance with the values given to you by the bootstrap script.

## Next Steps

- [Set up and test your first API](../set-up-first-api/)
- [Set up your portal and publish your first API](../set-up-portal-api/)


