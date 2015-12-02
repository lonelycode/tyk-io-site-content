+++
draft = false
title = "Tyk 1.9 - Know more, be better"
image = "/imgs/blog_tyk_1.9.png"
description = "Tyk v1.9 is ready, it's been a long time coming, but we've improved the UX, added more analytics, made the system more robust and performant and made everything more secure, and easier, to deploy"
date = 2015-12-07T09:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk", "v1.9"]
+++

I don't know about you, but it feels like it's been an age since we last got a release out the door. And we've been hard at work... making things, mysterious things. This year, for the holiday season, Tyk presents the glorious version 1.9 of our API Gateway and Management platform - a thunderous step forward in our mission to make the Internet a more connected place. 

Or, better yet, why don't I just tell you what we've been up to and skip the rhetoric?

This time round, we've concentrated on two things: **1. Making Tyk easier to install, manage, run and generally behave** and **2. Making Tyk more aware** (not Skynet aware... but that's coming in v2.0, watch this space).

#### For those who can't even, here's the TL;DR version:

- Tyk v1.9 now supports active uptime testing and polling of your service endpoints.
- Tyk will record uptime analytics for you including: average latency, errors, error type and success rates
- The host manager is no longer required, domain management is completely internalised in both the gateway and the dashboard
- We now have our own GPG-signed YUM and APT repositories and have secure deployment models for Ubuntu and RHEL
- Init scripts and configuration scripts are now included out of the box to make setup and configuration easier than ever
- A UX and design overhaul of the dashboard with plenty of bug fixes

Now... for the meat...

### Uptime Awareness

I'll start with awareness, v1.9 has something we're labeling "Uptime Awareness", we know many development teams have monitoring software that will check infrastructure, services and system health over time and wake some poor sod in the middle of the night with a blaring klaxon telling them that something is horribly, disastrously wrong, and in their bleary eyed confusion, rushing to their laptop, barely able to make out screen or sense, let alone remember their login details, they need to fix whatever just FUBAR'ed.

Which is great, in fact, it's wonderful. Us dark-loving, vampire-like developers love being up in the middle of the night to fix stuff.

Or... not. 

So that's where we came along and thought, you know what an API gateway should do? It should try to detect downtime, and immediately react to fix it, without needing to get anyone out of bed. That, in fact, was the basis of us adding our Circuit breaker functionality in the last version - self-reliance.. 

Now I don't want to over-simplify the awesome monitoring tools that exist today, you can use them to react to downtime and handle new instances / repairs and rollbacks through scripting, automated intervention and complex interactions, and they are far more feature rich than what Tyk offers. So to be clear, we are by no means trying to replace that.

Tyk's new uptime awareness is designed to enable you, the API owner, to write a bunch of test requests that will be polled at regular intervals by your gateway, and should downtime occur in one of those over a set sample rate and interval, Tyk can pull that host out of its load-balancer host list until it comes back up again. And in an ideal world, never exposing the downtime to the end users for any significant period.

But, if you insist, or are a particularly cruel team lead, Tyk can also wake up your SysOps and DevOps teams if you like, we'll send round some heavies to bust the door down and drag them out of bed by the hair... screaming. Ok, we won't do that, but we *can* send event notifications to your monitoring services to set off a chain reaction that could, potentially, send some heavies round (you've been warned, devs!).

But for us, it's all integrated. We wanted Tyk to be able to be reactive to downtime at the edge of the knife where it counts.

So, that's what we mean by increased awareness - naturally Tyk will collect analytics on your behalf for each of your API, version, and individual tests, storing average latency, error codes and success rates so you can get a retrospective look at how your APIs are doing.

Did we mention that this all integrates with tools like Etcd and Consul? Yeah - you can configure uptime awareness dynamically using service discovery tools if that's how you roll.

### Deployment, stability, performance etc.

The second big focus for us with this release is wanting Tyk to be "Well Behaved(tm)". Many of us who deploy software, release to servers or have to talk to the poor souls that manage releases every day, know that having a familiar deployment and installation pattern, and expected runtime behaviors to get things up and running is pretty great (and a relief). 

Having to deploy some awkward tarball and fiddle with directories and configuration files and symlinks sucks.

So we got our sh*t together and did something about it.

What did we do? 

#### The Host Manager is dead! Long live Integrated Domain Management!

We listened, and one of your biggest sources of confusion was Tyk's reliance on NginX to map to domains and have an easy to understand domain/route architecture... well no more! We took the host manager out back, thanked it for it's service, and kneecapped it. 

So the Host Manager, while it will still work and continues to be supported (and a valued member of the team), is no longer strictly *required*.

That means two less dependencies for Tyk, which is great from a "moving parts" perspective, while also making it really easy to set up and manage domain-based access and routing for your APIs using whatever structure you want for your dashboard, APIs and Developer Portal.

#### Dedicated, GPG-signed APT and YUM repositories and packages

When you install Tyk v1.9, you will most likely be doing it via our shiny new installation method.

First off, we have both YUM and APT repos, and those repositories are GPG signed, and secondly, if you are a RHEL user, the RPM binaries themselves are signed too. So you can be sure that the package you are installing comes from, and is approved by, us - and we deliver it on a silver platter across the chain-link, barbed-wire laden fence that is a secure HTTPS channel.

Secondly, because the installations follow standard Linux patterns (in fact, they are the same patterns you would use to install any more standard bit of Linux software), it makes it easy and straightforward to work with, or modify existing configurations for Puppet, Chef, SaltStack or other configuration management tools to get Tyk installed on your hosts.

We've run these installations on AWS EC2 Ubuntu LTS and RHEL 7, and they should work on any cloud-based linux host. 

Our new repositories currently have packages for:

- Debian Jessie
- Ubuntu 12.04 LTS
- Ubuntu 14.04 LTS
- Enterprise Linux 6
- Enterprise Linux 7

On ARM, i386 and amd64 architectures.

Naturally, we still have our Docker containers, and we've modified them now to be completely independent and make less assumptions about how they will be launched. This makes them much more modular and much easier to integrate with a microservices stack. 

#### Upstart, SysV, Systemd init scripts

Finally, sane init scripts! (Be still my beating hearts)...

You can now start both Tyk Gateway and Tyk Dashboard with standard init commands - this is great from a testing and management perspective as you can now start your host services the same way as you would nginx or Redis, as a server task, that behaves itself, that does what it says on the tin.

This might not sound like much, or be particularly "fun" or value added, but as a piece of server software it's important for us that Tyk can be installed, and run in a standard and familiar way, supporting whatever init system our developers prefer. For those tired-eyed SysOps that need to install stuff, lets make their lives a little easier.

#### Bootstrap and helper scripts

Configuring Tyk is not hard, but there's a few simple things that can trip up some users, and there's two clear modes that people want to run Tyk in - File based or Dashboard based. So why should we make it hard for you to get up and running? What gives us the right to make your life difficult? Exactly, we don't, so here you go.

Because of this, we've created some bootstrap / configuration scripts that can be used to generate configuration files that are ready to go for either mode, without the need to fiddle or tweak them in any way. 

### Bug fixes and UX

One thing you may notice, is that we've overhauled the dashboard look and feel, and fixed a few outstanding bugs that have been annoying our users - you can see the full fix list in the change log. But lets just say that things are a tighter, better put together, easier to use and more reliable than ever before.

Any questions, bribes or threats please address them to us on our GitHub page, or our Community forum.

Cheers,

Martin & the Tyk team
