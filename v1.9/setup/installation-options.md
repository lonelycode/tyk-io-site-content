+++
draft = false
title = "Installation Options"
date = 2014-07-29T10:59:33Z
[menu.sidebar_v1_9]
    parent = "setup"
    weight = -700
+++

Tyk supports multiple ways of installing and running, the main methods that we recommend are:

1. [Install Tyk on **Ubuntu** using our APT package repositories](../../setup/install-tyk-ubuntu/)
2. [Install Tyk on **Redhat** or **CentOS** using our RPM package repositories](../../setup/install-tyk-redhat-centos-el/)
3. [Install Tyk using our **Docker** images](../../setup/docker/)
3. [Try Tyk locally with our **Vagrant** guide](../../setup/vagrant-setup/)

Tyk is also supplied as tarballs on our Github downloads page, which can be manually installed and configured for your specific use case.

By default, Tyk Gateway and Tyk Dashboard are installed into the `/opt/ directory as:

- `/opt/tyk-dashboard` - The Tyk Dashboard application
- `/opt/tyk-gateway` - The Gateway application

### Manual installations

Manual installation can of course put the application anywhere, with Tyk Dashboard, one caveat is that it must know it's home directory, you can set this by adding a `home_dir` field to the `tyk_analytics.conf`.

To specify a cnofiguration to use with tyk, use the `--conf=filename.conf` parameter.

### Init scripts

Tyk ships with several init scripts, and it will attempt to install them in those systems that it detects, you will see which it has detected when the isntallation finishes.

To start Tyk is very simple:

	sudo service tyk-gateway start
	sudo service tyk-dashboard start

### Logs

Logs will be output to the default log directory of your preferred init system, usually `/var/log` or `/var/log/upstart`.

