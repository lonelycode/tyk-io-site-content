+++
draft = false
title = "Key Lifecycle Management"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -270
    parent="key-management"
+++

Core to managing your APIs is the ability to on-board developers and to migrate through various service tiers.

For example, you may have developers sign up to a free-tier of access to your underlying API, but a few months later, their requirements outgrow the tier they have enrolled in and demand more access.

In cases such as these you do not want to have to issue the developer a new token with a new policy tier that they can use, instead, you probably would rather just upgrade their tier.

Since keys in Tyk are hashed, you may also not have direct access tot he original token in order to manually change the policy for that token. Also, if you manually upgrade a policy on a key, it may not reflect in their developer profile, which is the core part of the service that identifies a developer to your organization.

## Updating a users existing key

The easiest way to update a user's policy is to go to their profile, and in the section marked "API Access", click on the "Change policy" link.

This will open a new modal window where you can select which policy to give the user access to.

When you submit this request, Tyk will securely process the hashed representation of the key to change their policy settings and make that change live.

This will also update the policy records that are associated with the developer so that you can always track which level of access your have given to which user on your system.


