+++
draft = false
title = "Install Tyk on Red Hat Enterprise Linux"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -500
+++

Tyk has it's own signed RPMs in a Yum repository hosted by the kind folks at [packagecloud.io](https://packagecloud.io), which makes it easy, safe and secure to install a trusted distribution of the Tyk Gateway stack.

This tutorial will run on an [Amazon AWS](http://aws.amazon.com) *Red Hat Enterprise Linux 7.1* instance. We will install both the Tyk Gateway and the Tyk Dashboard with all dependencies stored locally.

We're installing on a `t2.micro` because this is a demo, you'll need more RAM and more cores for better performance.

This configuration should also work (with some tweaks) for CentOS.

**Pre-requisites**:

- Ensure port `8080` is open: this is used in this guide for Gateway traffic (API traffic to be proxied)
- Ensure port `3000` is open: This is used by the dashboard to provide the GUI and the Developer Portal

## Installation steps:

### 1. Set up our YUM repositories:

First, we need to install some software hat allows us to use signed packages:

	sudo yum install pygpgme yum-utils wget
	
Next, we need to set up the various repository configurations for Tyk and MongoDB: 

#### Tyk gateway

Create a file named `/etc/yum.repos.d/tyk_tyk-gateway.repo` that contains the repository configuration below:

	[tyk_tyk-gateway]
	name=tyk_tyk-gateway
	baseurl=https://packagecloud.io/tyk/tyk-gateway/el/7/$basearch
	repo_gpgcheck=1
	enabled=1
	gpgkey=http://keyserver.tyk.io/tyk.io.rpm.signing.key
	       https://packagecloud.io/gpg.key
	sslverify=1
	sslcacert=/etc/pki/tls/certs/ca-bundle.crt
	
	[tyk_tyk-gateway-source]
	name=tyk_tyk-gateway-source
	baseurl=https://packagecloud.io/tyk/tyk-gateway/el/7/SRPMS
	repo_gpgcheck=1
	enabled=1
	gpgkey=http://keyserver.tyk.io/tyk.io.rpm.signing.key
	       https://packagecloud.io/gpg.key
	sslverify=1
	sslcacert=/etc/pki/tls/certs/ca-bundle.crt

#### Tyk dashboard

Create a file named `/etc/yum.repos.d/tyk_tyk-dashboard.repo` that contains the repository configuration below.

	[tyk_tyk-dashboard]
	name=tyk_tyk-dashboard
	baseurl=https://packagecloud.io/tyk/tyk-dashboard/el/6/$basearch
	repo_gpgcheck=1
	enabled=1
	gpgkey=http://keyserver.tyk.io/tyk.io.rpm.signing.key
	       https://packagecloud.io/gpg.key
	sslverify=1
	sslcacert=/etc/pki/tls/certs/ca-bundle.crt
	
	[tyk_tyk-dashboard-source]
	name=tyk_tyk-dashboard-source
	baseurl=https://packagecloud.io/tyk/tyk-dashboard/el/6/SRPMS
	repo_gpgcheck=1
	enabled=1
	gpgkey=http://keyserver.tyk.io/tyk.io.rpm.signing.key
	       https://packagecloud.io/gpg.key
	sslverify=1
	sslcacert=/etc/pki/tls/certs/ca-bundle.crt

#### Mongo DB

Create a /etc/yum.repos.d/mongodb-org-3.0.repo file so that you can install MongoDB directly, using yum.

	[mongodb-org-3.0]
	name=MongoDB Repository
	baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.0/x86_64/
	gpgcheck=0
	enabled=1

### EPEL

**What's this?** EPEL (Extra Packages for Enterprise Linux) is a free, community based repository project from Fedora which provides high quality add-on software packages for Linux distribution including RHEL, CentOS, and Scientific Linux. Epel isn't not a part of RHEL/CentOS but it is designed for major Linux distributions. In our case we need it for Redis

	wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
	
	sudo rpm -ivh epel-release-7-5.noarch.rpm
	
Finally we'll need to update our local cache, so run:

	sudo yum -q makecache -y --disablerepo='*' --enablerepo='tyk_tyk-dashboard' --enablerepo='tyk_tyk-gateway' --enablerepo=epel info zabbix

### 2. Install our packages:

We're ready to go, you can now install the relevant packages using yum:

	sudo yum install -y mongodb-org redis tyk-gateway tyk-dashboard 
	
*(you may be asked to accept the GPG key for our two repos and when the package installs, hit yes to continue)*

### 3. Start Mongo and Redis

In many cases Mongo and Redis will not be running, so lets start those:

	sudo service mongod start
	sudo service redis start

### 4. Configure Tyk Gateway

You can set up the core settings for Tyk Gateway with a single setup script, however for more involved deployments, you will want to provide your own configuration file, to get things running lets run:

	sudo /opt/tyk-gateway/install/setup.sh --dashboard=1 --listenport=8080 --redishost=localhost --redisport=6379 --domain="" --mongo=mongodb://127.0.0.1:27017/tyk_analytics

What we've done here is told the setup script that:

- `--dashboard=1`: We want to use the dashboard, so to enable all the flags for using MongoDB and Analytics tracking
- `--listenport=8080` Tyk should listen on port 8080 for API traffic
- `--redishost=localhost` Use redis on the hostname: localhost
- `--redisport=6379` Use the default redis port
- `--domain=""` Do not set a domain for the gateway, see the note on domains below for more about this
- `--mongo=mongodb://127.0.0.1:27017/tyk_analytics` Use this connection string to connect to the local MongoDB instance and use the `tyk_analytics` database

#### Pro Tip: Domains with Tyk Gateway

Tyk Gateway has full built in domain support, you can:

- Set Tyk to listen only on a specific domain for all API traffic
- Set an API to listen on a specific domain (e.g. api1.com, api2.com)
- Split APIs over a domain using a path (e.g. api.com/api1, api.com/api2, moreapis.com/api1, moreapis.com/api2 etc.)
- If you have set a hostname for the gateway, then all non-domain-bound APIs will be on this hostname + the `listen_path`

In this example, we don't want Tyk to listen on a single domain, and we can always set up custom domains at the API level in the dashboard. It is recommended to leave the Tyk gateway domain unbounded for flexibility and ease of deployment.

### 4. Configure Tyk Dashboard

We can set the dashboard up with a similar setup command, the below will get the dashboard set up for the local instance, **make sure to use the actual DNS hostname or the public IP of your instance as the last parameter**:

	sudo /opt/tyk-dashboard/install/setup.sh --listenport=3000 --redishost=localhost --redisport=6379 --mongo=mongodb://127.0.0.1:27017/tyk_analytics --tyk_api_hostname=$HOSTNAME --tyk_node_hostname=http://localhost --tyk_node_port=8080 --portal_root=/portal --domain="XXX.XXX.XXX.XXX"

What we have done here is:

- `--listenport=3000` Told Tyk Dashboard (and Portal) to listen on port 3000
- `--redishost=localhost` Tyk Dashboard should use the local redis instance
- `--redisport=6379` Tyk Dashboard should use the default port
- `--domain="XXX.XXX.XXX.XXX"` Bind the dashboard to the IP or DNS hostname of this instance (required)
- `--mongo=mongodb://127.0.0.1:27017/tyk_analytics` Use the local MongoDB (should always be the same as the gateway)
- `--tyk_api_hostname=$HOSTNAME` - Tyk Dashboard has no idea what hostname has been given to Tyk, so we need to tell it, in this instance we are just using the local HOSTNAME env variable, but you could set this to the public-hostname/IP of the instance
- `--tyk_node_hostname=http://localhost` - Tyk Dashboard needs to see a Tyk node in order to create new tokens, so we need to tell it where we can find one, in this case, use the one installed locally
- `--tyk_node_port=8080` Tell the dashboard that the Tyk node it should communicate with is on port 8080
- `--portal_root=/portal` - We want the portal to be shown on /portal of whichever domain we set for the portal

### 5. Start Tyk and Tyk dashboard:

Starting Tyk Gateway and the Tyk Dashboard is very easy, we supply our own run scripts which are compatible with most init systems - start the services with:

		sudo service tyk-gateway start
		sudo service tyk-dashboard start

### 6. Bootstrap the dashboard with an initial user:

When Tyk Dashboard is created for the first time, it has no initial user base or organisation to add data to, so we need to add this.

The best way to add this data is with the Admin API, to make it really easy we've supplied a bootstrap script that will set you up. If you want to customise it, take a look at the file in `/opt/tyk-dashboard/install/bootstrap.sh`

**To bootstrap your instance**:

	sudo /opt/tyk-dashboard/install/bootstrap.sh XXX.XXX.XXX.XXX

This command tells the bootstrap script to use the your IP as the base for the API calls, you can run the bootstrap remotely and change the first command line parameter to the DNS hostname of your instance.

You will now be able to log into and test your Tyk instance with the values given to you by the bootstrap script.

## Next Steps

- [Set up and test your first API](../set-up-first-api/)
- [Set up your portal and publish your first API](../set-up-portal-api/)

