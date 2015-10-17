+++
draft = false
title = "Mocking APIs and Importing API Blueprints"
date = 2014-07-29T09:31:36Z
[menu.sidebar_v1_4]
    weight = -290   
    
+++

Tyk now supports API mocking using it's new `use_extended_paths` versioning setup, to complement this feature, Tyk now also
supports an easy way to import [Apiary API Blueprints](https://apiblueprint.org/) in JSON format using the command line.

Blueprints can be imported and turned into standalone API definitions (for new APIs) and also imported as versions into 
existing APIs.

It is possible to import APIs and generate mocks or to generate White Lists that pass-through to an upstream URL.

Importing APIs is possible using Tyk's new command-line mode, it is also easy to do using the Tyk Dashboard interface.

All imported blueprints must be in the JSON representation of Blueprints markdown documents, this can be created using 
Apiary's [snowcrash tool](https://github.com/apiaryio/snowcrash).

Tyk outputs all new APi definitions to `stdout`, so redirecting the output to a file is advised 
in order to generate new definitions to use in a real configuration.

## Importing a blueprint as a new API:

Create a new definition from blueprint: 

`$> ./tyk --import-blueprint=blueprint.json --create-api --org-id=<id> --upstream-target="http://widgets.com/api/"`

## Importing a definition as a version in an existing API:

Add a version to a definition: 

`$> ./tyk --import-blueprint=blueprint.json --for-api=<path> --as-version="2.0"`

## Creating your API versions as a mock

As the API Blueprint definition allows for example responses to be embedded, these examples can be imported as forced replies,
in effect mocking out the API, to enable this mode, when generating a new API or importing as a version, 
simply add the `--as-mock` parameter.