+++
draft = false
title = "Docker"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -600
+++

Getting started with Tyk and Docker is very quick, we have set up a Docker Compose configuration that will get you up and running with a few commands. To get started with his tutorial, make sure you have both Docker and Docker Compose installed on your machine.

What we will do in this setup guide is build the entire stack (The gateway and the dashboard) in one go.

The way the Tyk docker images are set up is:

- Tyk Gateway container: This is a stand-alone container for the tyk gateway that manages your traffic
- Tyk Dashboard container: This is your dashboard and your portal

In older version of tyk, there would be a  third container, called Tyk Host manager, this would be managing and NginX instance to make portal and domain routing easier.

With this version of Tyk, domain handling is built into the main stack and no additional components are required to route to your portal except setting the domain name when you initialise your organisation.

## Working with docker

In order for everything to work in a single docker instance, assuming everything is on a single server, we will want all traffic to go through port 80, even though the dashboard runs on port 3000 and the gateway on port 8080.

**How we do this is:**

1. We set up a DNS instance in the docker compose container group, this will ensure we have two-way routing between the gateway and the dashboard images (if you don;t link your containers you won;t need this)
2. Run the Tyk Gateway so it can accept traffic on port 8080, but expose port 80 and map that so 80->8080
3. We override the tyk.conf file in the Tyk Gateway container with one that is specifically set up for this tutorial
3. Run the Tyk Dashboard on port 3000 and expose port 3000 (so you can see the dashboard)
4. Override the tyk_analytics.conf file with one that is specifically for a docker setup)
5. Run a setup script that will:

	- Create an organisation for you
	- Create a new user so you can log into the dashboard
	- Add three APIs to the gateway that proxy to your new organisations' portal, portal assets and public API
  - Create a portal home page with some dummy content

### Step 1. Set up some hosts entries

We are assuming that you are running this on a local docker installation, the Tyk Portal requires a domain name to bind to in order to work properly, so lets make sure we set that up:

	127.0.0.1    www.tyk-portal-test.com

OS X or Kitematic users should change this to their Docker Host IP Address, you can find this out by running `docker-machine ip default`.

This entry will be used to access our portal.

### Step 2. Get the quick start compose files

Our quick start is a github repository that contains everything you need to start Tyk, let's clone it locally:

	git clone git@github.com:lonelycode/tyk_quickstart.git
	cd tyk_quickstart

**A quick note for those using an older docker client (previous to docker client v1.9.0):** There is another YML file for older clients in the repository, rename this to `docker-compose.yml` for your setup to work.

### Step 3. Bootstrap your dashboard and portal

We've included a setup script that will create an organisation, a user and create the proxy configurations for your portal for you:

	docker-compose up -d
	./setup.sh

### Step 4. Log in

The setup script will provide login details for your dashboard - go ahead and log in.

#### One last thing for your portal:

If you wish to change your portal domain - **DO NOT USE** the drop-down option in the navigation, instead, change the domain names in the three site entries in the API section.

However, if you want clean URLs constructed for your APIs in the dashboard, setting this value will show the URLs for your APIs as relative to the domain you've set.

## Next Steps

- [Set up and test your first API](../../tutorial/set-up-first-api/)
- [Set up your portal and publish your first API](../../tutorial/set-up-portal-api/)
