![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST & Source Map Serialization Media Types
### API Blueprint's contract with machines

This document defines serialization formats of [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short and Source Map tree.

API Blueprint AST is a machine-friendly face of API Blueprint suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint.

API Blueprint Source Map is 1-on-1 with API Blueprint AST and represents the source maps for each of the nodes in the AST tree.

Converting API Blueprint to AST and its serialization is the task of API Blueprint Parser – [Snow Crash](https://github.com/apiaryio/snowcrash) or one of its [bindings](https://github.com/apiaryio/snowcrash#bindings). Reverse process from AST (serialization) to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## What?

**This document is intended for authors of tools using API Blueprint**. If you are looking for a way to describe your Web API proceed directly to [API Blueprint](https://github.com/apiaryio/api-blueprint).

## Version

+ **Version**: 3.0
+ **Created**: 2013-08-30
+ **Updated**: 2014-11-26

---

## Quick Links

+ [AST Description](#ast-description)
+ [Source map Description](#source-map-description)
+ [Media Types](#media-types)
+ [Keys description](#keys-description)
+ [Example: JSON serialization](#example-json-serialization)
+ [Serialization in Snow Crash](#serialization-in-snow-crash)
+ [Serialized Parsing Result Media Types][parsing media types]

---

## AST Description

Following is the description of API Blueprint AST media types using the [MSON](https://github.com/apiaryio/mson) syntax. The description starts with the top-level blueprint object.

### Blueprint

+ `_version` (string) - Version of the AST Serialization as [defined](#version) in this document
+ `metadata` (array) - Ordered array of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
    - (object)
        - `name` (string) - Name of the metadata
        - `value` (string) - Value of the metedata

+ `name` (string) - Name of the API
+ `description` (string) - Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` (array[[Resource Group](#resource-group)])
+ `dataStructures` (array[[Data Structures][]]) 

### Resource Group

Logical group of resources.

#### Properties

+ `name` (string) - Name of the Resource Group
+ `description` (string) - Description of the Resource Group (`.raw` or `.html`)
+ `resources` (array[[Resource](#resource)]) - Ordered array of the respective resources belonging to the Resource Group

### Resource

Description of one resource, or a cluster of resources defined by its URI template.

#### Properties

+ `name` (string) - Name of the Resource
+ `description` (string) - Description of the Resource (`.raw` or `.html`)
+ `uriTemplate` (string) - URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ([Payload](#payload)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter](#parameter)]) - Ordered array of URI parameters
+ `attributes` ([Attributes][]) - Description of the Resource attributes.

    In the case of a Resource with `name`, this section represents [MSON Named Type][].

+ `actions` (array[[Action](#action)]) - Ordered array of actions available on the resource each defining at least one complete HTTP transaction

### Action

An HTTP transaction (a request-response transaction). Actions are specified by an HTTP request method within a resource.

#### Properties

+ `name` (string) - Name of the Action
+ `description` (string) - Description of the Action (`.raw` or `.html`)
+ `method` (string) - HTTP request method defining the action
+ `parameters` (array[[Parameter](#parameter)]) - Ordered array of resource's URI parameters descriptions specific to this action
+ `attributes` ([Attributes][]) - Description of the action input attributes – the default properties of the request message-body.
+ `examples` (array[[Transaction Example](#transaction-example)]) - Ordered array of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload

An [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

#### Properties

+ `name` (string) - Name of the payload

    The content of this key depends on the type of payload:

    + **model** payload: name of the resource, if any
    + **request** payload: name of the request, if any
    + **response** payload: HTTP status code

+ `reference` ([Reference](#reference)) - Present if and only if a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) is present in the payload and it has been resolved correctly
+ `description` (string) - Description of the payload (`.raw` or `.html`)
+ `attributes` ([Attributes][]) - Description of the message-body attributes
+ `headers` (string) - Ordered array of HTTP headers that are expected to be transferred with HTTP message represented by this payload
+ `bodyAsset` ([Asset][]) - Message-body asset
+ `body` (string) 
    
    **Deprecated** – please use `bodyAsset` instead.

    _An entity body to be transferred with HTTP message represented by this payload._

+ `schemaAsset` ([Asset][]) - Validation schema for message-body asset. 
+ `schema` (string)

    **Deprecated** – please use `schemaAsset` instead.

    _A validation schema for the entity body as defined in `body`._

### Asset

An [API Blueprint asset][].

#### Properties
+ `asset` (string) - The Asset in its textual representation, as written in the source API Blueprint

+ `resolvedAsset` (string)

    `asset` property `string` as resolved by parser harness or subsequent tooling. Usually created from a [Data Structure][] description or fetching the Asset from an URL. 

### Parameter

Description of one URI template parameter.

#### Properties

- `description` (string) - Description of the parameter (`.raw` or `.html`)
- `type` (string) - An arbitrary type of the parameter (a string)
- `required` (string) - Boolean flag denoting whether the parameter is required (true) or not (false)
- `default` (string) - A default value of the parameter (a value assumed when the parameter is not specified)
- `example` (string) - An example value of the parameter
- `values` (array) - An array enumerating possible parameter values
    - (object)
        - `value` (string) - Value choice of for the parameter

### Transaction Example

An HTTP transaction example with expected HTTP message request and response payload(s).

#### Properties

+ `name` (string) - Name of the Transaction Example
+ `description` (string) - Description of the Transaction Example (`.raw` or `.html`)
+ `requests` (array[[Payload](#payload)]) - Ordered array of example transaction request payloads
+ `responses` (array[[Payload](#payload)]) - Ordered array of example transaction response payloads

### Reference

A reference object which is used whenever there is a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection).

#### Properties

+ `id` (string) - The identifier (name) of the reference

### Attributes ([Data Structure][])

Data structure definition as written in an API Blueprint [Attributes section][Attribute section].

> **Note:** The `name` property of the [Data Structure][]'s `type` property is only present when the respective [Attribute section][] defines an MSON named type, such as in the context of the [Resource section][]. Otherwise (in the case of a Payload, Action or an unnamed Resource) the Attributes section is considered to be anonymous named type.

### Data Structure

Definition of an [MSON][] data structure.

> **NOTE:** Properties of this object may use some types defined in the [MSON AST][]. 

#### Properties 
+ `type` ([Named Type][]) - Data Structure as described in the source API Blueprint

+ `resolvedType` ([Named Type][])

    `type` property [Named Type][] as resolved by parser harness or subsequent tooling. Usually created by expanding Type references.

    If present, this subtree MUST be a super set of the `type` property sub tree.

### Data Structures

List of arbitrary data structures defined in API Blueprint.

#### Properties
+ `types` (array[[Data Structure][]]) - Array of defined data structures

---

## Source Map Description
Following is the description of Source map media types using the [MSON](https://github.com/apiaryio/mson) syntax. The description starts with the top-level blueprint object.

### Source Map

An example source map.

- (array)
  - (array)
    - 1219 (number) - Zero-based index of the character position of the beginning of the source
    - 30 (number) - Length of the source
  - (array)
    - 1261 (number)
    - 175 (number)

### Blueprint Source Map

Source map of the [Blueprint](#blueprint).

#### Properties

+ `metadata` (array[[Source Map](#source-map)]) - An array of source maps where each item in metadata has its own source map
+ `name` ([Source Map](#source-map)) - Source map of API name
+ `description` ([Source Map](#source-map)) - Source map of API description
+ `resourceGroups` (array[[Resource Group Source Map](#resource-group-source-map)])

### Resource Group Source Map

Source map of the [Resource Group](#resource-group).

#### Properties

+ `name` ([Source Map](#source-map)) - Source map of name of the Resource Group
+ `description` ([Source Map](#source-map)) - Source map of description of the Resource Group
+ `resources` (array[[Resource Source Map](#resource-source-map)]) - Ordered array of the respective resources belonging to the Resource Group

### Resource Source Map

Source map of the [Resource](#resource).

#### Properties

+ `name` ([Source Map](#source-map)) - Source map of name of the Resource
+ `description` ([Source Map](#source-map)) - Source map of description of the Resource
+ `uriTemplate` ([Source Map](#source-map)) - Source map of URI Template
+ `model` ([Payload Source map](#payload-source-map)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter Source Map](#parameter-source-map)]) - Ordered array of source maps of URI parameters
+ `actions` (array[[Action Source Map](#action-source-map)]) - Ordered array of source maps of actions available on the resource each defining at least one complete HTTP transaction

### Action Source Map

Source map of the [Action](#action).

#### Properties

+ `name` ([Source Map](#source-map)) - Source map of name of the Action
+ `description` ([Source Map](#source-map)) - Source map of description of the Action
+ `method` ([Source Map](#source-map)) - Source map of HTTP request method defining the action
+ `parameters` (array[[Parameter Source Map](#parameter-source-map)]) - Ordered array of source maps of resource's URI parameters descriptions specific to this action
+ `examples` (array[[Transaction Example Source Map](#transaction-example-source-map)]) - Ordered array of source maps of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Source Map

Source map of [Payload](#payload). The source map of the payload is in fact the source map of the [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) when the reference is used.

#### Properties

+ `name` ([Source Map](#source-map)) - Source map of name of the payload
+ `reference` ([Source Map](#source-map)) - Source map of the reference, present if and only if a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) is present in the payload and it has been resolved correctly
+ `description` ([Source Map](#source-map)) - Source map of description of the payload
+ `headers` (array[[Source Map](#source-map)]) - Ordered array of source maps of HTTP headers that are expected to be transferred with HTTP message represented by this payload. Each item in the header has it's own source map.
+ `body` ([Source Map](#source-map)) - Source map of body to be transferred with HTTP message represented by this payload
+ `schema` ([Source Map](#source-map)) - Source map of a validation schema for the entity body as defined in `body`

### Parameter Source Map

Source map of [Parameter](#parameter).

#### Properties

- `description` ([Source Map](#source-map)) - Source map of description of the parameter
- `type` ([Source Map](#source-map)) - Source map of an arbitrary type of the parameter (a string)
- `required` ([Source Map](#source-map)) - Source map of boolean flag denoting whether the parameter is required (true) or not (false)
- `default` ([Source Map](#source-map)) - Source map of a default value of the parameter (a value assumed when the parameter is not specified)
- `example` ([Source Map](#source-map)) - Source map of an example value of the parameter
- `values` (array[[Source Map](#source-map)]) - Source map of an array enumerating possible parameter values. Each item has it's own source map.

### Transaction Example Source Map

Source map of [Transaction Example](#transaction-example).

#### Properties

+ `name` ([Source Map](#source-map)) - Source map of name of the Transaction Example
+ `description` ([Source Map](#source-map)) - Source map of description of the Transaction Example
+ `requests` (array[[Payload Source Map](#payload-source-map)]) - Ordered array of source maps of example transaction request payloads
+ `responses` (array[[Payload Source Map](#payload-source-map)]) - Ordered array of source maps of example transaction response payloads

---

> **NOTE:** Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details.

## Media Types

The `application/vnd.apiblueprint.ast` is the base media type for API Blueprint AST. An API Blueprint AST with raw Markdown descriptions has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html` suffix. The base media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats

Two supported, feature-equal serialization formats are JSON and YAML:

For the [API Blueprint AST](#ast-description)

+ `application/vnd.apiblueprint.ast.raw+json` and `application/vnd.apiblueprint.ast.html+json`
+ `application/vnd.apiblueprint.ast.raw+yaml` and `application/vnd.apiblueprint.ast.html+yaml`

For the [API Blueprint Source Map](#source-map-description)

+ `application/vnd.apiblueprint.sourcemap+json`
+ `application/vnd.apiblueprint.sourcemap+yaml`

### Example: JSON Serialization

`application/vnd.apiblueprint.ast.raw+json; version=3.0`

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
            "attributes": {
              "type": {
                "name": null,
                "base": {
                  "typeSpecification": {
                    "name": "<sub-type>"
                  }
                },
                "sections": null
              }
            },
            "bodyAsset": {
              "asset": "<resource model body>"
            },
            "schemaAsset": {
              "asset": "<resource model schema>"
            }
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
          "attributes": {
            "type": {
              "name": {
                "literal": "<resource name>"
              },
              "base": {
                "typeSpecification": {
                  "name": "<sub-type>"
                }
              },
              "sections": null
            }
          },
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
              "attributes": {
                "type": {
                  "name": null,
                  "base": {
                    "typeSpecification": {
                      "name": "<sub-type>"
                    }
                  },
                  "sections": null
                }
              },
              "examples": [
                {
                  "name": "<transaction example name>",
                  "description": "<transaction example description>",
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
                      "attributes": {
                        "type": {
                          "name": null,
                          "base": {
                            "typeSpecification": {
                              "name": "<sub-type>"
                            }
                          },
                          "sections": null
                        }
                      },
                      "bodyAsset": {
                        "asset": "<request body>"
                      },
                      "schemaAsset": {
                        "asset": "<request schema>"
                      }
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
                      "attributes": {
                        "type": {
                          "name": null,
                          "base": {
                            "typeSpecification": {
                              "name": "<sub-type>"
                            }
                          },
                          "sections": null
                        }
                      },
                      "bodyAsset": {
                        "asset": "<response body>"
                      },
                      "schemaAsset": {
                        "asset": "<response schema>"
                      }
                    }
                  ]
                }
              ]
            },
            {
              "name": "<action name>",
              "description": "<action description>",
              "method": "<action HTTP request method>",
              "parameters": [],
              "examples": [
                {
                  "name": "<transaction example name>",
                  "description": "<transaction example description>",
                  "responses": [
                    {
                      "name": "<response HTTP status code>",
                      "reference": {
                        "id": "<resource model name>"
                      },
                      "description": "<resource model description>",
                      "headers": [
                        {
                          "name": "<HTTP header field name>",
                          "value": "<HTTP header field value>"
                        }
                      ],
                      "bodyAsset": {
                        "asset": "<resource model body>"
                      },
                      "schemaAsset": {
                        "asset": "<resource model schema>"
                      }
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

`application/vnd.apiblueprint.sourcemap+json; version=2.1`

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
            },
            {
              "name": [
                [1219, 24]
              ],
              "description": [
                [1243, 22]
              ],
              "method": [
                [1219, 24]
              ],
              "parameters": [],
              "examples": [
                {
                  "name": [],
                  "description": [],
                  "responses": [
                    {
                      "name": [],
                      "reference": [
                        [1270, 24]
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

## Serialization in Snow Crash

The `snowcrash` [command-line tool](https://github.com/apiaryio/snowcrash#snow-crash-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) via the `--format` option.

Similarly, it also supports serialization of [API Blueprint Source Map](https://github.com/apiaryio/snowcrash/blob/master/src/BlueprintSourcemap.h) via the `--format` option if and only is the `-s` is present.

## Related Media Types

- [**Serialized Parsing Result Media Types**][parsing media types] - Media types for the serialization of complete parsing results (including warnings and errors)

## License

MIT License. See the [LICENSE](LICENSE) file.

<!-- API Blueprint AST -->

[parsing media types]: Parse%20Result.md

<!-- MSON -->

[MSON]: https://github.com/apiaryio/mson
[MSON AST]: https://github.com/apiaryio/mson-ast
[MSON Named Type]: https://github.com/apiaryio/mson/blob/master/MSON%20Specification.md#22-named-types
[MSON Named Types]: https://github.com/apiaryio/mson/blob/master/MSON%20Specification.md#22-named-types

<!-- MSON AST -->

[Named Type]: https://github.com/apiaryio/mson-ast#named-type
[Type Name]: https://github.com/apiaryio/mson-ast#type-name
[Type Definition]: https://github.com/apiaryio/mson-ast#type-definition
[Type Section]: https://github.com/apiaryio/mson-ast#type-section

[API Blueprint asset]: https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#asset

<!-- API Blueprint Attributes -->

[Attribute section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-attributes-section
[Resource section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-resource-section

<!-- Document anchors -->

[Asset]: #asset
[Attributes]: #attributes
[Data Structure]: #data-structure
[Data Structures]: #data-structures

