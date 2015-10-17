+++
draft = false
title = "Versioning"
date = 2014-07-29T10:57:08Z
[menu.sidebar]
    parent = "management"
    weight = -100
+++


Versioning an API with Tyk is very easy and should integrate easily with how your underlying API is set up. 


### Step 1

To activate versioning in an API, simply create a version entry in the `version_data.versions` section of the API Definition:

	{
		"version-1": {
			"name": "version-1",
			"expires": "",
			"paths": {
				"ignored": [],
				"white_list": [],
				"black_list": []
			}
		}
	}
	
### Step 2

And ensure that the `definition` section o the API Definition file/object is set up to find the version data:

    "definition": {
        "location": "header",
        "key": "x-api-version"
    }

In this section you can either set the location to `header` or `url-param`, this will tell Tyk where to try and find version information for the request. When
`url-param` is set, Tyk will check query parameters or url-form-encoded parameters for the version key (so GET, POST, PUT, and DELETE methods can be used 
with data encoded in the query string or in the body of the request).

When the key is extracted from the request, the current token session is checked to see whether the user has access to the version, this is checked by attempting to 
match the version name against the key value. *this is case sensitive*.

### Step 3

Finally, ensure that the API is actually set to allow versioning, this is done by setting the `version_data.not_versioned` value to `false`.

A few notes on versioning and allowing access:

- Version expiry is applied to all keys
- Version ignored / white-listing / black listing is applied to all keys
- Version access control is only applied to keys which have access-control parameters applied to them. If a key has no `access_rights` data in the session key, then the request will be allowed through to the underlying API.

Please see the [in-depth section](/api-management/api-definition-detail/) of this guide to read about what all of the keys in the API Definition files do.