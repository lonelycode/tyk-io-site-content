+++
draft = false
title = "Importing APIs"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_7]
    weight = -290   
    
+++

Tyk supports importing both API Blueprint and Swagger JSON definitions using the command line. tyk will output the converted file to to stdout. Below are the commands you can use to get Tyk to switch to command mode and generate the respective API definitions for both API Blue[rint and Swagger files.

## API Blueprint

Tyk now also supports an easy way to import [Apiary API Blueprints](https://apiblueprint.org/) in JSON format using the command line.

Blueprints can be imported and turned into standalone API definitions (for new APIs) and also imported as versions into 
existing APIs.

It is possible to import APIs and generate mocks or to generate White Lists that pass-through to an upstream URL.

Importing APIs is possible using Tyks new command-line mode, it is also easy to do using the Tyk Dashboard interface.

All imported blueprints must be in the JSON representation of Blueprints markdown documents, this can be created using 
Apiary's [snowcrash tool](https://github.com/apiaryio/snowcrash).

Tyk outputs all new API definitions to `stdout`, so redirecting the output to a file is advised 
in order to generate new definitions to use in a real configuration.

### Importing a blueprint as a new API:

Create a new definition from blueprint: 

`$> ./tyk --import-blueprint=blueprint.json --create-api --org-id=<id> --upstream-target="http://widgets.com/api/"`

### Importing a definition as a version in an existing API:

Add a version to a definition: 

`$> ./tyk --import-blueprint=blueprint.json --for-api=<path> --as-version="2.0"`

### Creating your API versions as a mock

As the API Blueprint definition allows for example responses to be embedded, these examples can be imported as forced replies,
in effect mocking out the API, to enable this mode, when generating a new API or importing as a version, 
simply add the `--as-mock` parameter.

## Swagger

Tyk now supports importing swagger documents to create API definitions and API versions. Swagger imports do not support mocking though, so sample data and replies will need to be added manually later.

### Importing a swagger document as a new API:

Create a new definition from swagger: 

`$> ./tyk --import-swagger=petstore.json --create-api --org-id=<id> --upstream-target="http://widgets.com/api/"`

### Importing a swagger document as a version into an existing API:

Add a version to a definition: 

`$> ./tyk --import-swagger=petstore.json --for-api=<path> --as-version="2.0"`

### Mocks

Tyk supports API mocking using the `use_extended_paths` versioning setup, and adding mocked URL data to one of the three list types (whit-list, black-list or ignored), in order to handle mocked path, use an entry that has the `action` property set to `reply`:

	...
    "ignored": [
        {
            "path": "/v1/ignored/with_id/{id}",
            "method_actions": {
                "GET": {
                    "action": "reply",
                    "code": 200,
                    "data": "Hello World\n",
                    "headers": {
                        "x-tyk-override": "tyk-override",
                    }
                }
            }
        }
    ], 
	...