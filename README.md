![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST
### JSON & YAML face of the API Blueprint
API Blueprint AST is JSON or YAML, machine-friendly, face of API Blueprint suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint.

This document defines serialization formats for [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short.

For the definition of API Blueprint AST Source Map see the [API Blueprint AST Source Map definition][Source Map Definition].

Converting API Blueprint to AST and its serialization is the task of API Blueprint Parser – [Snow Crash](https://github.com/apiaryio/snowcrash) or one of its [bindings](https://github.com/apiaryio/snowcrash#bindings). Reverse process from AST (serialization) to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## Don't like to design APIs in JSON or YAML?
If you are looking for a way to describe your Web API without using JSON or YAML see [API Blueprint](https://github.com/apiaryio/api-blueprint).

## Version
+ **Version**: 3.0
+ **Created**: 2013-08-30
+ **Updated**: 2014-12-16

## Quick Links
+ [AST Description](#ast-definition)
+ [Media Types](#media-types)
+ [Keys description](#keys-description)
+ [Example: JSON serialization](#example-json-serialization)
+ [Serialization in Snow Crash](#serialization-in-snow-crash)
+ [Serialized Parsing Result Media Types][parsing media types]

## AST Definition
Following is the definition of API Blueprint AST media types using the [MSON](https://github.com/apiaryio/mson) syntax. The definition starts with the top-level Blueprint object.


> **NOTE:** Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details.


### Blueprint (object)
+ `_version` (string) - Version of the AST Serialization as [defined](#version) in this document
+ `metadata` (array) - Ordered array of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
    - (object)
        - `name` (string) - Name of the metadata
        - `value` (string) - Value of the metadata

+ `name` (string) - Name of the API
+ `description` (string) - Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` (array[[Resource Group](#resource-group)])
+ `dataStructures` (array[[Data Structures][]])

### Resource Group (object)
Logical group of resources.

#### Properties
+ `name` (string) - Name of the Resource Group
+ `description` (string) - Description of the Resource Group (`.raw` or `.html`)
+ `resources` (array[[Resource](#resource)]) - Ordered array of the respective resources belonging to the Resource Group

### Resource (object)
Description of one resource, or a cluster of resources defined by its URI template.

#### Properties
+ `name` (string) - Name of the Resource
+ `description` (string) - Description of the Resource (`.raw` or `.html`)
+ `uriTemplate` (string) - URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ([Payload](#payload)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter](#parameter)]) - Ordered array of URI parameters
+ `attributes` ([Attributes][]) - Description of the Resource attributes.
+ `actions` (array[[Action](#action)]) - Ordered array of actions available on the resource each defining at least one complete HTTP transaction

### Action (object)
An HTTP transaction (a request-response transaction). Actions are specified by an HTTP request method within a resource.

#### Properties
+ `name` (string) - Name of the Action
+ `description` (string) - Description of the Action (`.raw` or `.html`)
+ `method` (string) - HTTP request method defining the action
+ `parameters` (array[[Parameter](#parameter)]) - Ordered array of resource's URI parameters descriptions specific to this action
+ `attributes` ([Attributes][]) - Description of the action input attributes – the default properties of the request message-body.
+ `examples` (array[[Transaction Example](#transaction-example)]) - Ordered array of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload (object)
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
+ `body` (string) - **Deprecated**

    An entity body to be transferred with HTTP message represented by this payload

    Note this property is **deprecated** and will be removed in a future. Use `assets/body/source` instead.

+ `schema` (string) - **Deprecated**

    A validation schema for the entity body as defined in `body`.

    Note this property is **deprecated** and will be removed in a future. Use `assets/schema/source` instead.

+ `assets`
  + `body` ([Asset][]) - An entity body to be transferred with HTTP message represented by this payload
  + `schema` ([Asset][]) - A validation schema for the entity body as defined in the `body`

### Asset (object)
An [API Blueprint asset][].

#### Properties
+ `source` (string)

    The Asset in its textual representation as written in the source API Blueprint

+ `resolved` (string)

    Asset in its textual form as resolved by parser's subsequent tooling. For example, generated from a [Data Structure][] description or by fetching the asset from an URL.

### Parameter (object)
Description of one URI template parameter.

#### Properties
- `name` (string) - Name of the parameter
- `description` (string) - Description of the parameter (`.raw` or `.html`)
- `type` (string) - An arbitrary type of the parameter (a string)
- `required` (string) - Boolean flag denoting whether the parameter is required (true) or not (false)
- `default` (string) - A default value of the parameter (a value assumed when the parameter is not specified)
- `example` (string) - An example value of the parameter
- `values` (array) - An array enumerating possible parameter values
    - (object)
        - `value` (string) - Value choice of for the parameter

### Transaction Example (object)
An HTTP transaction example with expected HTTP message request and response payload(s).

#### Properties
+ `name` (string) - Name of the Transaction Example
+ `description` (string) - Description of the Transaction Example (`.raw` or `.html`)
+ `requests` (array[[Payload](#payload)]) - Ordered array of example transaction request payloads
+ `responses` (array[[Payload](#payload)]) - Ordered array of example transaction response payloads

### Reference (object)
A reference object which is used whenever there is a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection).

#### Properties
+ `id` (string) - The identifier (name) of the reference

### Attributes ([Data Structure][])
Data structure definition as written in an API Blueprint [Attributes section][Attribute section].

### Data Structure (object)
Definition of an [MSON][] data structure.

> **NOTE:** Properties of this object may use some types defined in the [MSON AST][].

#### Properties
+ `source` ([Named Type][]) - The data structure as described in the source API Blueprint

+ `resolved` ([Named Type][])

    The data structure as resolved by parser's subsequent tooling. Usually obtained by expanding MSON Type references.

### Data Structures (object)
List of arbitrary data structures defined in API Blueprint.

#### Properties
+ `types` (array[[Data Structure][]]) - Array of defined data structures

## Media Types
The `application/vnd.apiblueprint.ast` is the base media type for API Blueprint AST. An API Blueprint AST with raw Markdown descriptions has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html` suffix. The base media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats
Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.ast.raw+json` and `application/vnd.apiblueprint.ast.html+json`
+ `application/vnd.apiblueprint.ast.raw+yaml` and `application/vnd.apiblueprint.ast.html+yaml`

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
              "source": {
                "name": null,
                "base": {
                  "typeSpecification": {
                    "name": "<sub-type>"
                  }
                },
                "sections": null
              }
            },
            "assets": {
              "body": {
                "source": "<resource model body>"
              },
              "schema": {
                "source": "<resource model schema>"
              }
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
            "source": {
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
                "source": {
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
                        "source": {
                          "name": null,
                          "base": {
                            "typeSpecification": {
                              "name": "<sub-type>"
                            }
                          },
                          "sections": null
                        }
                      },
                      "assets": {
                        "body": {
                          "source": "<request body>"
                        },
                        "schema": {
                          "source": "<request schema>"
                        }
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
                        "source": {
                          "name": null,
                          "base": {
                            "typeSpecification": {
                              "name": "<sub-type>"
                            }
                          },
                          "sections": null
                        }
                      },
                      "assets": {
                        "body": {
                          "source": "<response body>"
                        },
                        "schema": {
                          "source": "<response schema>"
                        }
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
                      "assets": {
                        "body": {
                          "source": "<resource model body>"
                        },
                        "schema": {
                          "source": "<resource model schema>"
                        }
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

## Related Media Types
- [**Serialized Parsing Result Media Types**][parsing media types] - Media types for the serialization of complete parsing results (including warnings and errors)

## Serialization in Snow Crash
The `snowcrash` [command-line tool](https://github.com/apiaryio/snowcrash#snow-crash-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) via the `--format` option. Similarly, it also supports serialization of [API Blueprint Source Map](https://github.com/apiaryio/snowcrash/blob/master/src/BlueprintSourcemap.h) using the `--format` and `--sourcemap` option.

## License
MIT License. See the [LICENSE](LICENSE) file.

[parsing media types]: Parse%20Result.md

[MSON]: https://github.com/apiaryio/mson
[MSON AST]: https://github.com/apiaryio/mson-ast
[MSON Named Type]: https://github.com/apiaryio/mson/blob/master/MSON%20Specification.md#22-named-types

[Named Type]: https://github.com/apiaryio/mson-ast#named-type
[Type Name]: https://github.com/apiaryio/mson-ast#type-name
[Type Definition]: https://github.com/apiaryio/mson-ast#type-definition
[Type Section]: https://github.com/apiaryio/mson-ast#type-section

[API Blueprint asset]: https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#asset

[Attribute section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-attributes-section
[Resource section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-resource-section

[Asset]: #asset
[Attributes]: #attributes-data-structure
[Data Structure]: #data-structure
[Data Structures]: #data-structures

[Source Map Definition]: Source%20Map.md
