+++
draft = false
title = "Docker"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
    weight = -600
+++

The fastest way to get started with Tyk and all of it's components is with Docker. So we recommend you follow the [instructions on installing docker](https://docs.docker.com/installation/) if you don't have it already.

## Step 1 - Set up some hosts entries

If you are using linux, you should be able to access your docker containers on 127.0.0.1, but for OSX users, your Docker instances will be in a VM and most likely have a different IP address. It's important to know what your Docker instance IP address is because you'll need it now.

Edit your `/etc/hosts` file and add the following lines:

	127.0.0.1 	testorg.tyk.docker
	127.0.0.1 	test-api.testorg.tyk.docker

You can also do this with [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) if you have it configured (set it up to handle the .docker domain), but this way will work for the demo. Note for OSX users the IP address would be to your Docker instance

The reason we need these two entries is because `testorg.tyk.docker` will eventually point at your portal and `test-api.testorg.docker` will point at your first proxied API.

## Step 2 - Run the setup script

The setup script works with docker and [docker-compose](https://docs.docker.com/compose/install/), you will need both running for this wto work. [OS X users should check out the Docker toolbox](https://www.docker.com/toolbox) as it contains everything you need.

In a shel that can see your docker instance, run:

	git clone git@github.com:lonelycode/tyk_quickstart.git
	cd tyk_quickstart
	docker-compose up -d tyk_nginx
	./setup.sh 

## Step 3 - Log in

Use the credentials given to you by the script to log into the dashboard. Congratulatons! tyk is now up and
running on your machine :-)

But it doesn't mean much if you want to actually want to test-drive all the features, so lets set up a portal and manage a basic API...

## Step 4 - Create a Portal and your first API

By default there is no portal configured, you need to configure it by visiting the portal settings screen, even if you don't want to change the option. Simply click on "Settings", the notification view on the right will say that no configuration was detected so it was created. Your portal won't work without this step, so it's important.

That's it, now you need to create the home page

### Create the home page

Go to "Pages" and Add a new page, give it any title you like (e.g. "Welcome") and then, at the bottom of the page, select "Make this page the home page"

Save the page.

### Create an API

Go to the "APIs" section, and select the green button to create a new API, Change the following fields:

1. API Name: `HttpBin`
2. Target: `http://httpbin.org`
3. Advanced Options -> API Slug: `test-api`

Save the API.

### Create a Policy

Under the Policies menu item, select "New Policy", you can leave all the defaults as is, except: 

1. Name the policy "Default"
2. Select the "HttpBin" API in the access control section and click "Add" so it appears in the list
3. Check the box that says "Make this policy active"

Save the policy by clicking the "Create" button.

### Publish the API to the portal

The API that you defined earlier is active and will work, however you don't have a key yet, you could manually create one in the "Keys" section, but it's bettter to use the portal flow to get your API key as a new developer. 

Not all APIs are visible to the portal, only the ones you tell it about, so under the "Catalogue" section, select "Add API", on the screen that apears, then:

1. Select your "Default" policy
2. Fill in the description fields
3. Ensure the "Enable this APi" checkbox is checked

Save the API Catalogue entry by clicking the "Update" button.

## Try out the gateway and portal

If all has worked, you should be able to browse to http://testorg.tyk.docker:8888/portal/ (or just click the "Your portal" link in the dashboard) and see the portal home page. You should also be able to sign up, log in and enroll for the HttpBin API.

Once you have enrolled for an API, you'll be given an auth token, you can try it out like this:

**Test Request**

	$ curl --header "authorization: {YOUR KEY}" http://test-api.testorg.tyk.docker:8888/get

Or you can use the directory-based URL format:

**Alternative test request:**

	$ curl --header "authorization: {YOUR KEY}" http://testorg.tyk.docker:8888/test-api/get

**Sample Response** 

	{
	  "args": {},
	  "headers": {
	    "Accept": "*/*",
	    "Accept-Encoding": "gzip",
	    "Authorization": "xxxxxxxxxxxxxxxxxxxxx",
	    "Host": "httpbin.org",
	    "User-Agent": "curl/7.37.1",
	    "X-Scheme": "http"
	  },
	  "origin": "xxx.xxx.xxx.xxx, xxx.xxx.xxx.xxx",
	  "url": "http://httpbin.org/get"
	}

That's it! You can now experiment with the various transforms, options and methods.

**Note:** For debugging purposes, it may be worth attaching to the dashboard and gateway containers so you can see the log output.

### The "Your Portal" link isn't working

Tyk tries to guess teh right IP address for the portal and the Tyk gateway instance, however this is usully not what you want. The way to set this correctly is to add a `hostname` entry in the `tyk_analytics.conf` file. For the demo, if you have set up `tyk.docker` to point at your docker host then all should work.
