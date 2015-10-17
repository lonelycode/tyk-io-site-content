+++
draft = false
title = "Organisations"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    weight = -300
    parent="security"
+++

As mentioned in the security overview, Organisations represent the basic unit of organisation that apply to Users and APIs (and their related data).

The concept of an organisation is important to remember when working with users in the dashboard, although organisations cannot be managed directly, they can be managed via the command line (adding them):

### Command Line - Add an Organisation

To add a new organisation via the command line, simply start the analytics application with the `--neworg` flag. Then follow the instructions on screen.

**Note:** due to a known bug, multi-ork organisation names are NOT supported, if you want to enter a multi-word Organisation name, please remember to enclose it in quotation marks: `"`.


