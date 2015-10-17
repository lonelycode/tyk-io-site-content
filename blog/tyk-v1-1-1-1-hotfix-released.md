+++
draft = false
title = "Tyk v1.1.1 Minor bugfix released"
description = "Minor bugfix release to make ignored paths more useful and get Tyk to behave better ina cluster"
date = 2014-10-04T14:30:00Z
author = "Martin Buhr"
categories = ["versions", "Tyk"]
+++

We just released a minor update to to the Tyk binary: v1.1.1, this version addresses two serious issues that affected Tyk installations.

First, Tyk will now enforce path checking in API versions before an auth check, this ensures ignored paths, blacklists and white lists can be enforced for open API's, when previously this was not possible, it also means that paths in the `ignored_path` list will simply pass through without any further checks - this raises a potential rate limiting issue with unprotected paths which we hope to address in v1.2.

Secondly, Tyk can be configured to gather analytics and not purge the redis DB, this could be dangerous (as you could run out of space), however if running Tyk in a clustered environment, then only one node should be doing the purging, so this change has been retrofitted from the current development branch into this release (many thanks to [Alex Whitman](https://github.com/alexwhitman) for the patch).

This version is fully compatible with the 0.7 release of the Tyk Dashboard.

You can download this release from our Downloads page.

