![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST & Sourcemap Serialization Media Types
### API Blueprint's contract with machines

This document defines serialization formats of [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short and Sourcemap tree.

API Blueprint AST is a machine-friendly face of API Blueprint suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint.

API Blueprint Sourcemap is 1-on-1 with API Blueprint AST and represents the source maps for each of the nodes in the AST tree.

Converting API Blueprint to AST and its serialization is the task of API Blueprint Parser – [Snow Crash](https://github.com/apiaryio/snowcrash) or one of its [bindings](https://github.com/apiaryio/snowcrash#bindings). Reverse process from AST (serialization) to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## What?

**This document is intended for authors of tools using API Blueprint**. If you are looking for a way to describe your Web API proceed directly to [API Blueprint](https://github.com/apiaryio/api-blueprint).

## Version

+ **Version**: 2.0
+ **Created**: 2013-08-30
+ **Updated**: 2014-10-02

---

## Quick Links

+ [AST Description](#ast-description)
+ [Sourcemap Description](#sourcemap-description)
+ [Media Types](#media-types)
+ [JSON serialization](#json-serialization)
+ [YAML serialization](#yaml-serialization)
+ [Keys description](#keys-description)
+ [Serialization in Snow Crash](#serialization-in-snow-crash)
+ [Serialized Parsing Result Media Types][parsing media types]

---

## AST Description
Following is the description of API Blueprint AST media types using the [MSON](https://github.com/apiaryio/mson) syntax. The description starts with the top-level blueprint object.

### Blueprint Object

+ `_version` (string) - Version of the AST Serialization as [defined](#version) in this document
+ `metadata` (array) - Ordered array of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
    - (object)
        - `name` (string) - Name of the metadata
        - `value` (string) - Value of the metedata

+ `name` (string) - Name of the API
+ `description` (string) - Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` (array: [Resource Group](#resource-group-object))

### Resource Group Object

Logical group of resources.

#### Attributes

+ `name` (string) - Name of the Resource Group
+ `description` (string) - Description of the Resource Group (`.raw` or `.html`)
+ `resources` (array: [Resource](#resource-object)) - Ordered array of the respective resources belonging to the Resource Group

### Resource Object

Description of one resource, or a cluster of resources defined by its URI template.

#### Attributes

+ `name` (string) - Name of the Resource
+ `description` (string) - Description of the Resource (`.raw` or `.html`)
+ `uriTemplate` (string) - URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ([Payload](#payload-object)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array: [Parameter](#parameter-object)) - Ordered array of URI parameters
+ `actions` (array: [Action](#action-object)) - Ordered array of actions available on the resource each defining at least one complete HTTP transaction

### Action Object

+ `name` (string) - Name of the Action
+ `description` (string) - Description of the Action (`.raw` or `.html`)
+ `method` (string) - HTTP request method defining the action
+ `parameters` (array: [Parameter](#parameter-object)) - Ordered array of resource's URI parameters descriptions specific to this action
+ `examples` (array: [Transaction Example](#transaction-example-object)) - Ordered array of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Object

An [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

#### Attributes

+ `name` (string) - Name of the payload

    The content of this key depends on the type of payload:

    + **model** payload: name of the resource, if any
    + **request** payload: name of the request, if any
    + **response** payload: HTTP status code

+ `description` (string) - Description of the payload (`.raw` or `.html`)
+ `headers` (string) - Ordered array of HTTP headers that are expected to be transferred with HTTP message represented by this payload
+ `body` (string) - An entity body to be transferred with HTTP message represented by this payload
+ `schema` (string) - A validation schema for the entity body as defined in `body`

### Parameter Object

Description of one URI template parameter.

#### Attributes

- `description` (string) - Description of the parameter (`.raw` or `.html`)
- `type` (string) - An arbitrary type of the parameter (a string)
- `required` (string) - Boolean flag denoting whether the parameter is required (true) or not (false)
- `default` (string) - A default value of the parameter (a value assumed when the parameter is not specified)
- `example` (string) - An example value of the parameter
- `values` (array) - An array enumerating possible parameter values
    - (object)
        - `value` (string) - Value choice of for the parameter

### Transaction Example Object

An HTTP transaction example with expected HTTP message request and response payload(s).

#### Attributes

+ `name` (string) - Name of the Transaction Example
+ `description` (string) - Description of the Transaction Example (`.raw` or `.html`)
+ `requests` (array: [Payload](#payload-object)) - Ordered array of example transaction request payloads
+ `responses` (array: [Payload](#payload-object)) - Ordered array of example transaction response payloads

## Sourcemap Description
Following is the description of Sourcemap media types using the [MSON](https://github.com/apiaryio/mson) syntax. The description starts with the top-level blueprint object.

### Source Map Object

An example source map.

- (array)
  - (array)
    - 1219 (number) - Start position of the source.
    - 30 (number) - Length of the source.
  - (array)
    - 1261 (number)
    - 175 (number)

### Blueprint Sourcemap Object

+ `metadata` (array: [Source Map](#source-map-object)) - An array of source maps where each item in metadata has its own source map
+ `name` ([Source Map](#source-map-object)) - Source map of API name.
+ `description` ([Source Map](#source-map-object)) - Source map of API description
+ `resourceGroups` (array: [Resource Group Sourcemap](#resource-group-sourcemap-object))

### Resource Group Sourcemap Object

Logical group of source maps of resources.

#### Attributes

+ `name` ([Source Map](#source-map-object)) - Source map of name of the Resource Group
+ `description` ([Source Map](#source-map-object)) - Source map of description of the Resource Group
+ `resources` (array: [Resource Sourcemap](#resource-sourcemap-object)) - Ordered array of the respective resources belonging to the Resource Group

### Resource Sourcemap Object

Source maps of one resource, or a cluster of resources defined by its URI template.

#### Attributes

+ `name` ([Source Map](#source-map-object)) - Source map of name of the Resource
+ `description` ([Source Map](#source-map-object)) - Source map of description of the Resource
+ `uriTemplate` ([Source Map](#source-map-object)) - Source map of URI Template
+ `model` ([Payload Sourcemap](#payload-sourcemap-object)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array: [Parameter Sourcemap](#parameter-sourcemap-object)) - Ordered array of source maps of URI parameters
+ `actions` (array: [Action Sourcemap](#action-sourcemap-object)) - Ordered array of source maps of actions available on the resource each defining at least one complete HTTP transaction

### Action Sourcemap Object

+ `name` ([Source Map](#source-map-object)) - Source map of name of the Action
+ `description` ([Source Map](#source-map-object)) - Source map of description of the Action
+ `method` ([Source Map](#source-map-object)) - Source map of HTTP request method defining the action
+ `parameters` (array: [Parameter Sourcemap](#parameter-sourcemap-object)) - Ordered array of source maps of resource's URI parameters descriptions specific to this action
+ `examples` (array: [Transaction Example Sourcemap](#transaction-example-sourcemap-object)) - Ordered array of source maps of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Sourcemap Object

A Source map of [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

#### Attributes

+ `name` ([Source Map](#source-map-object)) - Source map of name of the payload
+ `description` ([Source Map](#source-map-object)) - Source map of description of the payload
+ `headers` (array: [Source Map](#source-map-object)) - Ordered array of source maps of HTTP headers that are expected to be transferred with HTTP message represented by this payload. Each item in the header has it's own source map.
+ `body` ([Source Map](#source-map-object)) - Source map of body to be transferred with HTTP message represented by this payload
+ `schema` ([Source Map](#source-map-object)) - Source map of a validation schema for the entity body as defined in `body`

### Parameter Object

Description of one URI template parameter.

#### Attributes

- `description` ([Source Map](#source-map-object)) - Source map of description of the parameter
- `type` ([Source Map](#source-map-object)) - Source map of an arbitrary type of the parameter (a string)
- `required` ([Source Map](#source-map-object)) - Source map of boolean flag denoting whether the parameter is required (true) or not (false)
- `default` ([Source Map](#source-map-object)) - Source map of a default value of the parameter (a value assumed when the parameter is not specified)
- `example` ([Source Map](#source-map-object)) - Source map of an example value of the parameter
- `values` (array: [Source Map](#source-map-object)) - Source map of an array enumerating possible parameter values. Each item has it's own source map.

### Transaction Example Object

A source map of HTTP transaction example with expected HTTP message request and response payload(s).

#### Attributes

+ `name` ([Source Map](#source-map-object)) - Source map of name of the Transaction Example
+ `description` ([Source Map](#source-map-object)) - Source map of description of the Transaction Example
+ `requests` (array: [Payload Sourcemap](#payload-sourcemap-object)) - Ordered array of source maps of example transaction request payloads
+ `responses` (array: [Payload Sourcemap](#payload-sourcemap-object)) - Ordered array of source maps of example transaction response payloads

---

> **NOTE:** Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details.

## Media Types

The `application/vnd.apiblueprint.ast` is the base media type for API Blueprint AST. An API Blueprint AST with raw Markdown descriptions has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html` suffix. The base media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats

Two supported, feature-equal serialization formats are JSON and YAML:

For the [API Blueprint AST](#ast-description)

+ `application/vnd.apiblueprint.ast.raw+json` and `application/vnd.apiblueprint.ast.html+json`
+ `application/vnd.apiblueprint.ast.raw+yaml` and `application/vnd.apiblueprint.ast.html+yaml`

For the [API Blueprint Sourcemap](#sourcemap-description)

+ `application/vnd.apiblueprint.sourcemap+json`
+ `application/vnd.apiblueprint.sourcemap+yaml`

### JSON Serialization

`application/vnd.apiblueprint.ast.raw+json; version=2.0`

```json
{
  "_version": "<AST version>",
  "metadata": [
    {
      "name": "<metadata key name>",
      "value": "<metadata value>"
    }
  ],
  "name": "<API name>",
  "description": "<API description>",
  "resourceGroups": [
    {
      "name": "<resource group name>",
      "description": "<resource group description>",
      "resources": [
        {
          "name": "<resource name>",
          "description": "<resource description>",
          "uriTemplate": "<resource URI template>",
          "model": {
            "name": "<resource model name>",
            "description": "<resource model description>",
            "headers": [
              {
                "name": "<HTTP header field name>",
                "value": "<HTTP header field value>"
              }
            ],
            "body": "<resource model body>",
            "schema": "<resource model schema>"
          },
          "parameters": [
            {
              "name": "<name>",
              "description": "<description>",
              "type": "<type>",
              "required": "<required parameter flag>",
              "default": "<default value>",
              "example": "<example value>",
              "values": [
                {
                  "value": "<enum element>"
                }
              ]
            }
          ],
          "actions": [
            {
              "name": "<action name>",
              "description": "<action description>",
              "method": "<action HTTP request method>",
              "parameters": [
                {
                  "name": "<name>",
                  "description": "<description>",
                  "type": "<type>",
                  "required": "<required parameter flag>",
                  "default": "<default value>",
                  "example": "<example value>",
                  "values": [
                    {
                      "value": "<enum element>"
                    }
                  ]
                }
              ],
              "examples": [
                {
                  "name": "<transaction example name>",
                  "description": "<transaction example name>",
                  "requests": [
                    {
                      "name": "<request name>",
                      "description": "<request description>",
                      "headers": [
                        {
                          "name": "<HTTP header field name>",
                          "value": "<HTTP header field value>"
                        }
                      ],
                      "body": "<request body>",
                      "schema": "<request schema>"
                    }
                  ],
                  "responses": [
                    {
                      "name": "<response HTTP status code>",
                      "description": "<response description>",
                      "headers": [
                        {
                          "name": "<HTTP header field name>",
                          "value": "<HTTP header field value>"
                        }
                      ],
                      "body": "<response body>",
                      "schema": "<response schema>"
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

`application/vnd.apiblueprint.sourcemap+json; version=2.0`

```json
{
  "metadata": [
    [
      [0, 39]
    ]
  ],
  "name": [
    [39, 13]
  ],
  "description": [
    [52, 19]
  ],
  "resourceGroups": [
    {
      "name": [
        [71, 30]
      ],
      "description": [
        [101, 30]
      ],
      "resources": [
        {
          "name": [
            [131, 46]
          ],
          "description": [
            [177, 24]
          ],
          "uriTemplate": [
            [131, 46]
          ],
          "model": {
            "name": [
              [131, 46]
            ],
            "description": [
              [214, 29]
            ],
            "headers": [
              [
                [267, 56]
              ]
            ],
            "body": [
              [344, 26]
            ],
            "schema": [
              [393, 28]
            ]
          },
          "parameters": [
            {
              "name": [
                [441, 81]
              ],
              "description": [
                [441, 81]
              ],
              "type": [
                [441, 81]
              ],
              "required": [
                [441, 81]
              ],
              "default": [
                [441, 81]
              ],
              "example": [
                [441, 81]
              ],
              "values": [
                [
                  [552, 19]
                ]
              ]
            }
          ],
          "actions": [
            {
              "name": [
                [572, 24]
              ],
              "description": [
                [596, 22]
              ],
              "method": [
                [572, 24]
              ],
              "parameters": [
                {
                  "name": [
                    [637, 81]
                  ],
                  "description": [
                    [637, 81]
                  ],
                  "type": [
                    [637, 81]
                  ],
                  "required": [
                    [637, 81]
                  ],
                  "default": [
                    [637, 81]
                  ],
                  "example": [
                    [637, 81]
                  ],
                  "values": [
                    [
                      [748, 19]
                    ]
                  ]
                }
              ],
              "examples": [
                {
                  "name": [],
                  "description": [],
                  "requests": [
                    {
                      "name": [
                        [770, 24]
                      ],
                      "description": [
                        [798, 22]
                      ],
                      "headers": [
                        [
                          [844, 56]
                        ]
                      ],
                      "body": [
                        [921, 19]
                      ],
                      "schema": [
                        [963, 21]
                      ]
                    }
                  ],
                  "responses": [
                    {
                      "name": [],
                      "description": [
                        [1029, 23]
                      ],
                      "headers": [
                        [
                          [1076, 56]
                        ]
                      ],
                      "body": [
                        [1153, 20]
                      ],
                      "schema": [
                        [1196, 22]
                      ]
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

### YAML Serialization

`application/vnd.apiblueprint.ast.raw+yaml; version=2.0`

```yaml
_version: <AST version>

metadata:
- name: "<metadata key name>"
  value: "<metadata value>"

name: "<API name>"
description: "<API description>"

resourceGroups:
- name: "<resource group name>"
  description: "<resource group description>"

  resources:
  - name: "<resource name>"
    description: "<resource description>"
    uriTemplate: "<resource URI template>"

    model:
      name: "<resource model name>"
      description: "<resource model description>"

      headers:
      - name: "<HTTP header field name>"
        value: "<HTTP header field value>"

      body: "<resource model body>"
      schema: "<resource model schema>"

    parameters:
    - name: "<name>"
      description: "<description>"
      type: "<type>"
      required: "<required parameter flag>"
      default: "<default value>"
      example: "<example value>"
      values:
      - value: "<enum element>"

    actions:
    - name: "<action name>"
      description: "<action description>"
      method: "<action HTTP request method>"

      parameters:
      - name: "<name>"
        description: "<description>"
        type: "<type>"
        required: "<required parameter flag>"
        default: "<default value>"
        example: "<example value>"
        values:
        - value: "<enum element>"

      examples:
      - name: "<transaction example name>"
        description: "<transaction example name>"

        requests:
        - name: "<request name>"
          description: "<request description>"

          headers:
          - name: "<HTTP header field name>"
            value: "<HTTP header field value>"

          body: "<request body>"
          schema: "<request schema>"

        responses:
        - name: "<response HTTP status code>"
          description: "<response description>"

          headers:
          - name: "<HTTP header field name>"
            value: "<HTTP header field value>"

          body: "<response body>"
          schema: "<response schema>"
```

`application/vnd.apiblueprint.sourcemap+yaml; version=2.0`

```yaml
metadata:
-
  -
    - 0
    - 39
name:
-
  - 39
  - 13
description:
-
  - 52
  - 19
resourceGroups:
- name:
  -
    - 71
    - 30
  description:
  -
    - 101
    - 30
  resources:
  - name:
    -
      - 131
      - 46
    description:
    -
      - 177
      - 24
    uriTemplate:
    -
      - 131
      - 46
    model:
      name:
      -
        - 131
        - 46
      description:
      -
        - 214
        - 29
      headers:
      -
        -
          - 267
          - 56
      body:
      -
        - 344
        - 26
      schema:
      -
        - 393
        - 28
    parameters:
    - name:
      -
        - 441
        - 81
      description:
      -
        - 441
        - 81
      type:
      -
        - 441
        - 81
      required:
      -
        - 441
        - 81
      default:
      -
        - 441
        - 81
      example:
      -
        - 441
        - 81
      values:
      - 
        -
          - 552
          - 19
    actions:
    - name:
      -
        - 572
        - 24
      description:
      -
        - 596
        - 22
      method:
      -
        - 572
        - 24
      parameters:
      - name:
        -
          - 637
          - 81
        description:
        -
          - 637
          - 81
        type:
        -
          - 637
          - 81
        required:
        -
          - 637
          - 81
        default:
        -
          - 637
          - 81
        example:
        -
          - 637
          - 81
        values:
        - 
          -
            - 748
            - 19
      examples:
      - name: []
        description: []
        requests:
        - name:
          -
            - 770
            - 24
          description:
          -
            - 798
            - 22
          headers:
          -
            -
              - 844
              - 56
          body:
          -
            - 921
            - 19
          schema:
          -
            - 963
            - 21
        responses:
        - name: []
          description:
          -
            - 1029
            - 23
          headers:
          -
            -
              - 1076
              - 56
          body:
          -
            - 1153
            - 20
          schema:
          -
            - 1196
            - 22
```

## Serialization in Snow Crash

The `snowcrash` [command-line tool](https://github.com/apiaryio/snowcrash#snow-crash-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) and [API Blueprint Sourcemap](https://github.com/apiaryio/snowcrash/blob/master/src/BlueprintSourcemap.h) via the `--format` option.

## Related Media Types

- [**Serialized Parsing Result Media Types**][parsing media types] - Media types for the serialization of complete parsing results (including warnigns and errors)

## License

MIT License. See the [LICENSE](LICENSE) file.


[parsing media types]: Parse%20Result.md
