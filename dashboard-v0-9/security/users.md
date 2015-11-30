+++
draft = false
title = "Managing Users"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -290
    parent="security"
+++

Users that are managed in the Dashboard have access to the dashboard (and in turn, can edit other users). Fro the Users screen it i possible to add, edit and revoke user access as well as change a user's password.

### The User view

![Tyk user view](/imgs/user-view.png)

In this view it is possible to:

- Update the user First and Last name
- Edit their email address (this will also change their username to access the dashboard)
- Set their active state - this allows users to remain in the system, but have access revoked
- View and reset their API key, these keys can be used with the advanced management API

### Reseting a user password

To reset a user password, click the "Reset password" button and enter a new password. Passwords are encrypted in the DB.

### Deleting a user

To delete a user, select the "Delete User" button, after confirming your action the user (and their credentials) will b revoked for the system. For security purposes it is advised to de-activate and reset the users API key before deletion just in case.

### Quick Revoke

It is also possible to de-activate a user from the user list view, simply select the "Revoke" button beside the users entry and their access to the dashboard will be stopped.



