+++
draft = false
title = "Vagrant"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -400
+++

There are quite a few moving parts to Tyk, Tyk Dashboard and the hot reload manager, so below is a step-by-step guide on
getting Tyk setup in a vagrant instance, with the dashboard and the host manager (if you have a license, skip these steps if you don't)...

## 1. Create a folder for testing:

    #> mkdir tyktest && cd tyktest

## 2. Create a vagrant image:

    #> vagrant init


## 3. Configure the Vagrantfile

A new file called Vagrantfile has been created, we need to expose port 3000 and use the precise64 base box (we assume you've got this,
otherwise download it), to do replace the following lines in the default vagrant file with the ones listed:

    orig:  config.vm.box = "base"
    new:   config.vm.box = "precise64"

    and

    orig: config.vm.network :forwarded_port, guest: 80, host: 8080
    new:  config.vm.network :forwarded_port, guest: 3000, host: 3000
    add:  config.vm.network :forwarded_port, guest: 5000, host: 5000

## 4. Now, you can spin up your vagrant instance and ssh to it:

    #> vagrant up && vagrant ssh


## 5. Install Mongo and Redis

In order for everything to work, you'll need MongoDB and Redis installed, this can be done as follows (instructions accurate at time of writing):

    vagrant@precise64:~$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
    vagrant@precise64:~$ echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
    vagrant@precise64:~$ sudo apt-get update
    vagrant@precise64:~$ sudo apt-get install mongodb-org redis-server vim

This could take a while as there's a lot to install, but it will set up a default MongoDB instance and Redis instance on your vagrant box,
obviously you wouldn't do this in production.

## 6. The environment is ready, now to install Tyk!

First off, get the 1.3 (latest) Tyk release, since we are using the precise64 box we want the AMD64 release, we'll use the `.deb` install
because that is the fastest method:

    vagrant@precise64:~$ wget http://bit.ly/tyk-v1-6-amd64-deb
    vagrant@precise64:~$ sudo dpkg -i tyk.linux.amd64_1.6-1_all.deb

## 7. Update the configuration file

With Tyk installed, we now need to make some changes to the configuration file (we assume you know vim, of course you could use any editor you like):

    vagrant@precise64:~$ sudo vim /etc/tyk/tyk.conf

Replace the boilerplate in the file with the following (you can cut and paste this whole block to replace what is in the file):

    {
        "listen_port": 5000,
        "secret": "352d20ee67be67f6340b4c0605b044b7",
        "template_path": "/etc/tyk/templates",
        "use_db_app_configs": true,
        "app_path": "/etc/tyk/apps/",
        "storage": {
            "type": "redis",
            "host": "localhost",
            "port": 6379,
            "username": "",
            "password": ""
        },
        "enable_analytics": true,
        "analytics_config": {
            "type": "mongo",
            "csv_dir": "/tmp",
            "mongo_url": "mongodb://localhost/tyk_analytics",
            "mongo_db_name": "tyk_analytics",
            "mongo_collection": "tyk_analytics",
            "purge_delay": 10,
            "ignored_ips": []
        },
       "health_check": {
         "enable_health_checks": true,
         "health_check_value_timeouts": 60
       }

    }

What we've done is tell Tyk to listen on port 5000, using the default secret (NOT recommended for production), changed the template path and app
path to the `/etc/tyk/` folders and told Tyk to use database app configs instead of file-based ones. Notice that the storage and analytics config
settings have been set to localhost and the type set to Mongo. We've also set a 10 second purge delay.

## 8. Tyk is ready to go

You can now (in a screen or in a new terminal) run:

    vagrant@precise64:~$ tyk

Tyk should now be running and (hopefully) not show any errors. Tyk has no apps configured and will not proxy anything until
you start feeding it database-based app definitions and hot-reload it. In order to do that we will need the dashboard and the host manager.

## 9. Download and unzip the Tyk dashboard package:

Lets get the dashboard tarball and get everything up and running

    vagrant@precise64:~$ wget http://bit.ly/tyk-dashboard-v-0-9-4-amd64-tar-gz
    vagrant@precise64:~$ tar -xvzf tyk-dashboard-amd64-v0.9.4.tar.gz
    vagrant@precise64:~$ cd tyk-analytics-v0.9.4

## 10. Start Tyk

It is recommended to start Tyk in a separate terminal or screen as we will be running 3 processes: Tyk, Tyk Host Manager and Tyk Dashboard.

    vagrant@precise64:~/$ tyk

## 11. You're set up and should be ready to get started

Lets set you up with a user, on the command line, in the Tyk dashboard folder:

    vagrant@precise64:~/tyk-analytics-v0.9.4$ ./tyk-analytics --neworg --newuser

Follow the on-screen instructions. **Note: For the organisation name pleas use a SINGLE word, as there is a known bug if you want to use a two-word name.
OR place a multi-word organisation name in quotation marks - e.g. if your Organsation is called `John Smith limited`, use `"John Smith Limited"`.

When the user creation process starts, choose the organisation that the user is associated with, and then set up a password for them,
the email address will be your username.

You will only need to do this once to create the first admin user.

## 15. The service has started, let's take a look:

Point your browser at `http://localhost:3000` and you should be presented wit the Tyk Dashboard login screen.

You should now be able to create a new API, new users and view your stats - to check this out, create an Open (Keyless) API that points at
google.com or something else, which is un-versioned and has en empty expiry field. Tyk will generate an API ID for you which you can then
test by pointing your browser at:

    http://localhost:5000/{{api-id}}/

You should be proxied to the target domain.

Enjoy!
