+++
draft = false
title = "Key changes in v1.4"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_4]
    weight = -300
home = "1"
+++

## Changes to Tyk 

In version 1.4 we focussed mainly on extending the flexibility of the gateway behyond webhooks and event handlers. Tyk version 1.4 now
has full support for scripting your own middleware and event handlers. We've done this by embedding a JavaScript interpreter into the aplication which
has a fixed set of external API's (to increase usability) and key TykJS namespace modules to make the creation of middleware and event hanlers very simple.

Another key change in this version, beyond bug-fixes, is the ability to use the tyk REST API to add new API Definitions to Tyk and have those flushed to disk.
The `/tyk/apis/` endpoint now supports `POST`, `PUT` and `DELETE` methods so you can add new definitions to your stack on the fly. 


## Changes to Tyk Dashboard (v0.9.2)

The dashboard has only undergone minor bug-fixes and is fully compliant with Tyk. The biggest addition is the re-introduction of the Basic Auth authentication 
method in the API Definition view.

## The full change log for version 1.4 is:

- Added expiry TTL to `tykcommon`, data expiry headers will be added to all analytics records, set `expire_analytics_after` to `0` to have data live indefinetely (currently 100 years), set to anything above zero for data in MongoDB to be removed after x seconds. **requirement**: You must create an expiry TTL index on the tyk_analytics collection manually (http://docs.mongodb.org/manual/tutorial/expire-data/). If you do not wish mongo to manage data warehousing at all, simply do not create the index.
- Added a JS Virtual Machine so dynamic JS middleware can be run PRE and POST middleware chain
- Added a global JS VM
- Added an `eh_dynamic_handler` event handler type that runs JS event handlers
- Added Session management API and HttpRequest API to event handler JSVM.
- Added JS samples
- Fixed a bug where requests that happened at identical times could influence the quota wrongly
- Changed default quota behaviour: On create or update, key quotas are reset. *unless* a new param `?suppress_reset=1` accompanies the REST request. This way a key can be updated and have the quote in Redis reset to Max, OR it can be edited without affecting the quota
- Rate limiter now uses new Redis based rate limiting pattern
- Added a `?reset_quota=1` parameter check to `/tyk/orgs/key` endpoint so that quotas can be reset for organisation-wide locks
- Organisations can now have quotas
- Keys and organisations can be made inactive without deleting

You can download the latest version of Tyk from our downloads page.
