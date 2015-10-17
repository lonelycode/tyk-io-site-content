+++
draft = false
title = "Running Tyk with Docker"
image = "/imgs/tyk-docker.png"
description = "We've added docker support, and we've streamlined the demo experience, here's how to get started super quickly with Tyk The Dashboard and the portal on Docker."
date = 2015-04-30T09:30:00Z
author = "Martin Buhr"
categories = ["Tyk", "Tyk Dashboard", "Docker"]
+++

It's here! Dockerised Tyk, and it's very exciting :-)

This change has made it incredibly easy to get started with Tyk, we've also tried to make sure with these containers that they follow best practice where possible, and make sure that the experience using Tyk is as painless as possible.

There's still quite a few steps, but thatnks to Dockers linking capabilities, there's far less dependency management and everything jsut runs. So, let's get started:

**Update** We've made a handy little gist that will get everything up and running for you without having to perform the steps below. the only thing you'll really want to have in place is `dnsmasq` to handle the .docker domain DNS on your system. The gist is a bash script, and *should* work on OSX and linux so long as docker is instaled. [See the quickstart shell script here](https://gist.github.com/lonelycode/4f645c4733faaa74d8fd).

After you've run the script, follow the instructions from Step 5 to create your portal and test APIs.

### Step 1 - Get your basics set up

For this demo to work, we're going to use `dnsmasq` on your local machine, because a full Tyk install requires dynamic subdomains for your local host, [there's a really good guide on doing this on OSX here](http://passingcuriosity.com/2013/dnsmasq-dev-osx/), and for linux, [it's even easier](https://www.computersnyou.com/3786/how-to-setup-dnsmasq-local-dns/).

Once you have `dnsmasq` installed and have your OS using it to look up domains (for this demo, we are assuming the TLD `.docker` is being managed by `dnsmasq`), we can get started with the whole docker thing.

### Step 2 - Get the main components installed:

A full tyk install does quite a lot of things, you have:

1. NginX to manage domain routing for APIs and the portal
2. The gateway to manage and proxy traffic
3. The dashboard to manage users, APIs, the Portal and Policies
4. The host manager to ensure hot-reloads and insertions of new API's are reflected by your dmain routing
5. Redis to store keys
6. mongo to store analytics

So to get started, lets start the base services:

	$ docker run -d --name tyk_redis redis && docker run -d --name tyk_mongo mongo

Now we can add the gateway, it won't do much yet, but it's a core component

	$ docker run -d --name tyk_gateway -p 8080:8080 --link tyk_redis:redis --link tyk_mongo:mongo tykio/tyk-gateway

Note that the mongo and redis instances here are ephemeral, if you stop them, your keys and configurations will be lost, you can get round this by mounting the data directories (see the docs for the relevant flags)

### Step 3 Adding a user to your gateway

For the gateway, isince this is a first time install, we need to create an organisation and a new user (for this doc, we are assuming your organisation slug will be `test-org`), so lets do that:

	$ docker run -i --name tyk_dashboard -p 3000:3000 --link tyk_redis:redis --link tyk_gateway:tyk_gateway --link tyk_mongo:mongo tykio/tyk-dashboard /bin/bash

	$ ./tyk-analytics --neworg --newuser

	$ CTRL-C

If CTRL-C doesn't exit the container, manually stop and remove it using 'docker stop tyk_gateway && docker rm tyk_gateway'

### Step 4 Starting it all up

Assuming you have your DNS set up, we should just be able to kick off the remainign components:

First we start the dashboard:

	$ docker run -d --name tyk_dashboard -p 3000:3000 --link tyk_redis:redis --link tyk_gateway:tyk_gateway tykio/tyk-dashboard

Then we start the host maanger and nginx:

	$ docker run -d --name tyk_nginx -p 8888:80 --link tyk_gateway:tyk_gateway --link tyk_dashboard:tyk_dashboard --link tyk_mongo:tyk_mongo --link tyk_redis:tyk_redis -e DOMAINALIAS=tyk.docker tykio/tyk-host-manager

### Step 5. Set up a demo API and your portal

You now have all of the components running, and if all worked as it should your dashbaord should be available on `http://127.0.0.1:3000` (or the IP of your docker VM if you are on OSX)

You can now log into your dashboard with the credentials you created in step 3. 

#### Your first API

Lets set up a test APi that point at the excellent [httpbin](http://httpbin.org) service, first, select "Apis" from the right hand menu.

Press the "Add new APi button" and fill in the next screen with the following details:

1. API Name: `HttpBin Test`
2. Target URL: `http://httbin.org`

Select the "Advanced Options" tab and add:

1. API Slug: "httpbin"

Click "Save"

Congratulations, you've jsut created your first API.

### Enable the portal

To set up the portal, all you need to do is:

1. Open the "Settings" screen for the portal, you shouldn;t need to change anything, but opening also creates a default set of settings for you
2. Under the POlicies page, use "Create policy" to set up a simple policy, the defaults should suffice, though will need to specify access to the `httpin` API under the "Access Rights" section, name this policy "Default" and save it
3. Under "Catalogue" - select "Add new API" and fill in the fields, remember to select tthe HttpBin Api you just created, the "Default" policy you just defined and remember to select "enable this API". Save the API
3. Under "Pages" create a new page, title it "Welcome" and check the "Make this the home page" checkbox, save the page.

Your portal is ready - to visit it, you can go to `http://test-org.tyk.docker:8888/portal/`

If everything is set up correctly  the portal welcome home page tempalte should show up. 

### Step 6 - Get started as a developer

You can now sign up using the portal as a developer, then once signed up, you can use your new login details to enroll for your test API.

If everything is working, you should now be given an API key, **remember to save this as it will be the last time you can see it**.

#### Step 7 - Try out your new API key

The httpbin API will be on `http://httpbin.test-org.tyk.docker:8888`, try it out with curl:

	$ curl --header "authorization: 5540ded9f79a20000800000188895e702a0044885f06d64ef8418fbe" http://httpbin.test-org.tyk.docker:8888/get

	{
	  "args": {},
	  "headers": {
	    "Accept": "*/*",
	    "Accept-Encoding": "gzip",
	    "Authorization": "5540ded9f79a20000800000188895e702a0044885f06d64ef8418fbe",
	    "Host": "httpbin.org",
	    "User-Agent": "curl/7.37.1",
	    "X-Scheme": "http"
	  },
	  "origin": "172.17.1.30, 62.232.114.250",
	  "url": "http://httpbin.org/get"
	}

If you go to the portal's developer dashboard, you should also see your usage chart update.

Congratulations, you are ready to go!