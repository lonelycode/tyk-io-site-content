+++
draft = false
title = "Quickstart"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
    weight = -700
+++

Tyk is very flexible, so we have developed several setup guides depending on what you are trying to achieve, and how you want to run Tyk.

This quickstart will give you a whistle-stop tour of the key components of a Tyk setup: The dashboard, the portal and the gateway.

To get you started as fast as possible we're using our Tyk Cloud Hybrid setup as this gives you every component instantly without having to install anything but a single Docker container. This will install the Tyk gateway locally, and let you control it from our dashboard management interface as well as experiment with the portal.

**If you want to install the full stack locally, [click here](../docker/), otherwise, follow the steps below:**

### Step 1. Get an account

Go to our Tyk Cloud Signup page, and [sign up for an account](https://cloud.tyk.io/signup)

### Step 2. Get your credentials

1. Go to https://admin.cloud.tyk.io and login with your new details
2. Click "Users" and select your name, you will see your RPC credentials, take note of these:


	![RPC Creds](/imgs/quickstart-creds.png)


### Step 3. Get Tyk installed

Next you will need to run our start script, it's very straightforward, simply run the command below in a terminal that can see your Docker daemon:

For OSX Users, open a CLI that can access the `docker` command:

	user@localhost:~/$ curl "https://raw.githubusercontent.com/lonelycode/tyk-hybrid-docker/master/start.sh" -o "start.sh"
	user@localhost:~/$ chmod +x start.sh
	user@localhost:~/$ ./start.sh 8080 54636453636 [RPC-CREDENTIALS] [API CREDENTIALS]

For Linux users:

	user@localhost:~/$ wget https://raw.githubusercontent.com/lonelycode/tyk-hybrid-docker/master/start.sh
	user@localhost:~/$ chmod +x start.sh
	user@localhost:~/$ sudo ./start.sh 8080 54636453636 [RPC-CREDENTIALS] [API CREDENTIALS]

### Step 4. Create an API

Go to the "APIs" section in your Tyk Cloud Dashboard, and select the green button to create a new API. Change the following fields:

1. API Name: `HttpBin`
2. Target: `http://httpbin.org`
3. API Slug: `test-api`
4. Save the API
5. When you return to the API list, **copy the API-ID of the API you've created, you'll need that later:**

	![API ID](/imgs/registered-apis.png)

### Step 5. Create a Policy

Policies enable you to control the quotas and limits of groups of keys that are part of the policy. Policies are integral to using the portal, but can be safely ignored. You can always create keys manually and set their quotas on the key themselves.

Under the Policies menu item, select "New Policy". You can leave all the defaults as is, except:

1. Name the policy "Default"
2. Select the "HttpBin" API in the access control section and click "Add" so it appears in the list
3. Check the box that says "Make this policy active"

Save the policy by clicking the "Create" button.

### Step 6. Publish the API to the portal

The API that you defined earlier is active and will work, however you don't have a key yet, you could manually create one in the "Keys" section, but it's better to use the portal flow to get your API key as a new developer.

An API catalogue entry combines a description of your underlying service(s) with a policy that a developer can subscribe to. Since a policy can give access to multiple services this enables you to have a flexible way to create API "Plans" that grant different levels of access to your services.

Not all APIs are visible to the portal, only the ones you tell it about. So under the "Catalogue" section, select "Add API" then:

1. Select your "Default" policy
2. Fill in the description fields
3. Ensure the "Enable this API" checkbox is checked

Save the API Catalogue entry by clicking the "Update" button.

### Step 7. Try it out!

If all has worked, you should be able to browse to `https://[your-org-slug].cloud.tyk.io/portal/` and see the portal home page. You should also be able to sign up, log in and enroll for the HttpBin API via the API catalogue.

Once you have enrolled for an API, you'll be given an auth token, you can try it out now. Remember that API ID we saved earlier? You can use it locally now (**make sure to replace the items in angled brackets with your own values**):

**Test Command**

	$ curl --header "authorization: {YOUR KEY}" http://{YOUR-DOCKER-IP}:8080/{API-ID}/get

**Output**

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


### My keys won't update

Hybrid setups work by caching a copy of keys in your local Redis database, if you are looking to invalidate a token (e.g. you delete or change it from the cloud instance), then it will still be available on your local instance for at least 30 seconds until the change propagates to your cluster.

A new key will become available instantly, but any changes or deletions can take up to 30s to fully propagate.

To delete the key manually, use the Tyk REST API to remove it from your hybrid instance as follows:

	$ curl -X DELETE --header "x-tyk-authorization: {YOUR SECRET}" http://{YOUR-DOCKER-IP}:8080/tyk/keys/{KEY-ID}?api_id={{API_ID}}

### You're all set up, now experiment with some of the cool features

There's detailed documentation for each of the features that Tyk offers - **[check out the breakdown of here](/dashboard-v0-9/api-management/api-management-overview/)**
