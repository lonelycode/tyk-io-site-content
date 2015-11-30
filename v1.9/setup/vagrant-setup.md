+++
draft = false
title = "Vagrant"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -400
+++

Tyk v1.9 has a streamlined quickstart setup for Vagrant, this demo works with Ubuntu Precise and makes use of direct-pipes to bash from a web script, we **do not recommend** doing any of this on a prouction machine.

Full, detailed instructions on how to [install Tyk on Ubunutu Server (LTS)]() or how to [install Tyk on Red hat Enterprise linux (RHEL)]() are available and go through the entire process manually.

Given this caveat, lets get you up and running.

This demo will install Tyk Gateway and Tyk Dashboard on an Ubuntu box in a vagrant instance, we are going to do a little bit of hosts hacking to make your portal URL work properly, but apart from that this is a standard installation.

## Installation Steps

### 0. Initial steps

Edit your `/etc/hosts` file to give yourself a domain for your portal:

	127.0.0.1       my-awesome-portal.com

Later, when you have logged into your Dashboard, you can use "Your Developer Portal" -> "Set your portal domain" to set it to this value.

### 1. Create a folder for testing:

    mkdir tyktest && cd tyktest

### 2. Create a vagrant image:

    vagrant init


### 3. Configure the Vagrantfile

A new file called Vagrantfile has been created, we need to expose port 3000 and use the precise64 base box (we assume you've got this,
otherwise download it), to do replace the following lines in the default vagrant file with the ones listed:

    orig:  config.vm.box = "base"
    new:   config.vm.box = "hashicorp/precise64"

    and

    orig: config.vm.network :forwarded_port, guest: 80, host: 8080
    new:  config.vm.network :forwarded_port, guest: 3000, host: 3000
    add:  config.vm.network :forwarded_port, guest: 8080, host: 8080

### 4. Spin up your vagrant instance and ssh to it:

    vagrant up && vagrant ssh


### 5. Install Mongo and Redis

In order for everything to work, you'll need MongoDB and Redis installed, this can be done as follows (instructions accurate at time of writing):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
    
    echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
    
    sudo apt-get update
    
    sudo apt-get install mongodb-org redis-server vim curl

This could take a while as there's a lot to install, but it will set up a default MongoDB instance and Redis instance on your vagrant box, we don;t recommend doing this in production.

### 6. The environment is ready, now to install Tyk!

We're goingt o install Tyk the fast and dirty way, using a special script that detects your environment and handles the full installation from our APT repository:

	curl -s https://packagecloud.io/install/repositories/tyk/tyk-gateway/script.deb.sh | sudo bash
	
	curl -s https://packagecloud.io/install/repositories/tyk/tyk-dashboard/script.deb.sh | sudo bash
	
	sudo apt-get install tyk-gateway tyk-dashboard
		
Your terminal will update as dependencies and packages are installed, at the end you will be able to bootstrap your new Tyk services.

### 7. Configure Tyk Gateway

Configuring Tyk is very easy, we've provided a handy script that gets you going, from which you can then personalise your Tyk installation, first off, let's configure Tyk Gateway:

	sudo /opt/tyk-gateway/install/setup.sh --dashboard=1 --listenport=8080 --redishost=localhost --redisport=6379 --domain="" --mongo=mongodb://localhost/tyk_analytics
	
What we've done here is told the setup script that:

- `--dashboard=1`: We want to use the dashboard, so to enable all the flags for using MongoDB and Analytics tracking
- `--listenport=8080` Tyk should listen on port 8080 for API traffic
- `--redishost=localhost` Use redis on the hostname: localhost
- `--redisport=6379` Use the default redis port
- `--domain=""` Do not set a domain for the gateway, see the note on domains below for more about this
- `--mongo=mongodb://localhost/tyk_analytics` Use this connection string to connect to the local MongoDB instance and use the `tyk_analytics` database

### 8. Configure Tyk Dashboard

We can set the dashboard up with a similar setup command, the below will get the dashboard set up for the local instance, **make sure to use the actual public IP of your vagrant instance as the last parameter**:

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
- `--portal_root=/portal` - We want the portal to be shown on /portal of whichever domain we set for the portal

### 5. Start Tyk and Tyk dashboard:

		sudo service tyk-gateway start
		sudo service tyk-dashboard start

### 6. Bootstrap the dashboard with an initial user and organisation:

When Tyk Dashboard is created for the first time, it has no initial user base or organisation to add data to, so we need to add this.

The best way to add this data is with the Admin API, to make it really easy we've supplied a bootstrap script that will set you up. If you want to customise it, take a look at the file in `/opt/tyk-dashboard/install/bootstrap.sh`

**To bootstrap your instance**:

	sudo /opt/tyk-dashboard/install/bootstrap.sh XXX.XXX.XXX.XXX

This command tells the bootstrap script to use the localhost as the base for the API calls, you can run the bootstrap remotely and change the first command line parameter to the DNS hostname of your instance.

You will now be able to log into and test your Tyk instance with the values given to you by the bootstrap script.

## Next Steps

- [Set up and test your first API](../set-up-first-api/)
- [Set up your portal and publish your first API](../set-up-portal-api/)