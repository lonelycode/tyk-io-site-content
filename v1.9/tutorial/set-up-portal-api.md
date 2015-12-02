+++
draft = false
title = "Set up your portal"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "tutorial"
    weight = -500
+++

### Step 1 - Create your first API

If you haven't already, [create an API](../../tutorial/set-up-first-api/), this tutorial assumes you've created your first API using the dashboard and called it "Test API".

### Step 2 - Initialise all your portal settings

By default there is no portal configured, you need to configure it by visiting the portal settings screen, **even if you don't want to change the options**. Simply click on "Portal Management" -> "Settings", the notification view on the right will say that no configuration was detected so it was created. Your portal won't work without this step, so it's important.

That's it, now you need to create the home page

### Step 3. Create the home page

Go to "Pages" and Add a new page, give it any title you like (e.g. "Welcome") and then, select the "Default Home Page Template" from the page Type section and then, at the bottom of the page, ensure "Make this page the home page" is set.

Save the page.

### Step 4. Create a Policy

When you publish an API to the portal, Tyk actually publishes a way for developers to enroll in a policy, not the API directly.

**Why?** A Tyk policy can grant access to multiple APIs (so long as they all use the same access control mechanism) and set a template for any keys that are generated for the portal for things such as Tags, Rate Limits and Quotas. Another useful feature with a policy and the portal is that when the key is generated for a developer, it can be made to expire after a certain time - e.g. a trial key.

To create a policy for your test API, uUnder the Policies menu item, select "New Policy", you can leave all the defaults as is, except:

1. Name the policy "Default"
2. Select the "Test API" API in the access control section and click "Add" so it appears in the list
3. Check the box that says "Make this policy active"

Save the policy by clicking the "Create" button.

### Step 5. Publish the API to the portal

The API that you defined earlier is active and will work as you've seen in th previous tutorial, this time we want to use the Portal to generate a token for a named developer.

Not all APIs are visible to the portal, only the ones you tell it about, so under the "Catalogue" section, select "Add API", on the screen that appears, then:

1. Select your "Default" policy
2. Fill in the description fields
3. Ensure the "Enable this API" checkbox is checked

Save the API Catalogue entry by clicking the "Update" button.

### Step 6. Set your portal hostname

When you set up your Tyk installation, you will have had to at some point define a hostname for your portal, either as a `/etc/hosts` file entry, or as a qualified hostname such as portal.domain.com. To make the Dashboard aware of this:

1. Select "Your Developer portal"
2. Select "Set Your Portal Domain"
3. Enter the hostname here and wait for Tyk to refresh

This process will ind your organisations' portal to the domain name you've specified. If you select "Your developer portal" -> "Open your portal", a new page will open with your new (most likely empty) portal home page.

**A note for Docker users**

If you are using docker, do not use the drop-down, instead, use the domain name you defined when you set up the forward proxy for your domains - if you followed the docker setup guide, your dashboard will be on: `www.tyk-portal-test.com`.

## Step 6. Try it out

If all has worked, you should now be able to view your Developer portal, select the "API Catalogue" in the main navigation. You should also be able to sign up, log in and enroll for the Test API.

Once you have enrolled for an API, you'll be given an auth token, you can try it out like this:

**Test Request**

	$ curl --header "authorization: {YOUR KEY}" http://{{YOUR-GATEWAY-HOST}}:8888/test-api/get

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

