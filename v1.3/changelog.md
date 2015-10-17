+++
draft = false
title = "Key changes in v1.3"
date = 2014-07-29T09:31:36Z
categories = ["home"]
[menu.sidebar_v1_3]
    weight = -300
home = "1"
+++

## Changes to Tyk

Version 1.3 expands on previous releases by focusing on extensibility and transparency. The biggest new feature is the ability 
to mock out API's and their responses as part of our new extended versioning support (more on that below) and the ability to 
import API blueprint documents to generate new API Definitions and versions, making it easier than ever to manage ongoing 
API development.

This is made possible because of our new extended versioning support, which makes it possible for administrators to create new
API white lists, black lists and ignored paths that filter based on method as well as upstream path, as well as overriding 
responses for those paths if necessary (perhaps the upstream API is misbehaving, mocking out the endpoint on the 
gateway with a standard error response is a fast and effective way to prevent further damage by ongoing requests).

To increase transparency, Tyk now supports health checks for API administrators, which gives second-by-second access to upstream latency average, requests per second, throttles per second, 
quota violations per second and key failure events per second. This wil make it easier to monitor how well your upstream API 
is performing as well as integrating core performance data into any scaling / monitoring infrastructure you may have set up.

We've also made it easier for end-users to get access to their quota status without impacting their actual quotas, we've done 
this by adding a new endpoint to all APIs that will return detailed info on the users current quota. Tyk will also embed 
headers in replies that give the user request-by-request feedback on their key details.

## Changes to Tyk Dashboard

Tyk Dashboard now supports the extended versioning setup for APIs, an import function for API blueprints as part of the UI and 
key bug fixes. All charts in the dashboard will now report full upstream API averages, chart data can now be 
activated and deactivated for cleaner reporting.

New users will be happy to know that the redis database password requirement is no longer a requirement, making it easier to 
quickly set up new installations.

Overall UI improvements throughout to mkae the interface more responsive and intuitive.

## The full change log for version 1.3 is:

- Tyk now records upstream API latency as part of it's default analytics setup (Mongo DB output only)
- It is now possible to set IP's that shouldn't be tracked by analytics by setting the `ignored_ips` flag in the config file (e.g. for health checks) 
- Many core middleware configs moved into tyk common, tyk common can now be cross-seeded into other apps if necessary and is go gettable.
- Added a health-check function, calling `GET /tyk/health` with an `api_id` param, and the `X-Tyk-Authorization` header will return upstream latency average, requests per second, throttles per second, quota violations per second and key failure events per second. Can be easily extended to add more data.
- Tyk now reports quota status in response headers (Issue #27)
- Calling `/{api-id}/tyk/rate-limits` with an authorised header will return the rate limit for the current user without affecting them. Fixes issue #27
- Extended path listing (issue #16) now enabled, legacy paths will still work. You can now create an extended path set which supports forced replies (for mocking) as well as limiting by method, so `GET /widget/1234` will work and `POST /windget/1234` will not.
- You can now import API Blueprint files (JSON format) as new version definitions for your API, this includes mocking out responses. Blueprints can be added to existing API's as new versions or generate independent API definitions. 
  - Create a new definition from blueprint: `./tyk --import-blueprint=blueprint.json --create-api --org-id=<id> --upstream-target="http://widgets.com/api/"`
  - Add a version to a definition: `./tyk --import-blueprint=blueprint.json --for-api=<api_id> --as-version="2.0"`
  - Create a mock for either: use the `--as-mock` parameter.
- More test coverage throughout