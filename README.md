![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST

## Deprecation Notice

As of 2015-12-02 the API Blueprint AST is deprecated and should not be used for new development. The API Blueprint AST has been superseded by [API Elements](https://github.com/apiaryio/api-elements).

### JSON & YAML face of the API Blueprint
API Blueprint AST is JSON or YAML, machine-friendly, face of API Blueprint suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint.

This document defines serialization formats for [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short.

For the definition of API Blueprint AST Source Map see the [API Blueprint AST Source Map definition][Source Map Definition].

Converting API Blueprint to AST and its serialization is the task of API Blueprint Parser – [Drafter](https://github.com/apiaryio/drafter) or one of its [bindings](https://github.com/apiaryio/drafter#bindings). Reverse process from AST (serialization) to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## Don't like to design APIs in JSON or YAML?
If you are looking for a way to describe your Web API without using JSON or YAML see [API Blueprint](https://github.com/apiaryio/api-blueprint).

## Version
+ **Version**: 4.0
+ **Created**: 2013-08-30
+ **Updated**: 2015-09-07

## Future Development
As of version 3.0 the API Blueprint AST is in the *interim* transition period
towards unified document-structure based on the concept of DOM elements
(see the [Element][] object). Please refrain from using AST elements marked as
*"deprecated"*.

## Quick Links
+ [AST Description](#ast-definition)
+ [Media Types](#media-types)
+ [Keys description](#keys-description)
+ [Example: JSON serialization](#example-json-serialization)
+ [Serialization in Drafter](#serialization-in-drafter)
+ [Serialized Parsing Result Media Types][parsing media types]

## AST Definition
Following is the definition of API Blueprint AST media types using the [MSON](https://github.com/apiaryio/mson) syntax.

> **NOTE:** Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details.

### Blueprint (object)
+ `_version` (string) - Version of the AST Serialization as [defined](#version) in this document
+ `metadata` (array) - Ordered array of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
    - (object)
        - `name` (string) - Name of the metadata
        - `value` (string) - Value of the metadata

+ `name` (string) - Name of the API
+ `description` (string) - Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` (array[[Resource Group][]]) - **Deprecated**

    Note this property is **deprecated** and will be removed in a future.
    Replaced by `category` elements of the `content` property.

+ `element`: `category` (fixed, required)
+ `content` (array[[Element][]]) - Section elements of the blueprint

### Element (object)
A component of an API description.

#### Properties
+ `element` (enum[string]) - Element name
    + `category` - Element is a group of other elements
    + `copy` - Element is a human readable text
    + `resource` - Element is a Resource
    + `dataStructure` - Element is a Data Structure definition
    + `asset` - Element is an asset of API description
+ `attributes` (object) - Element-specific attributes
    + `name` (string, optional) - Human readable name of the element
+ `content` (enum)
    + (array[[Element][]]) - Ordered array of nested elements (for element `category`)
    + (string) - Markdown-formatted text (for element `copy` and `asset` type)
    + ([Resource][]) - Resource definiton (for element `resource`)
    + ([Data Structure][]) - Data structure (for element `dataStructure`)

### Resource Group (object)
**Deprecated**, replaced by the `category` [Element][].

Logical group of resources.

#### Properties
+ `name` (string) - Name of the Resource Group
+ `description` (string) - Description of the Resource Group (`.raw` or `.html`)
+ `resources` (array[[Resource][]]) - Ordered array of the respective resources belonging to the Resource Group

### Resource (object)
Description of one resource, or a cluster of resources defined by its URI template.

#### Properties
+ `name` (string) - Name of the Resource
+ `description` (string) - Description of the Resource (`.raw` or `.html`)
+ `element`: `resource` (fixed, required)
+ `uriTemplate` (string) - URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ([Payload][]) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter][]]) - Ordered array of URI parameters
+ `actions` (array[[Action][]]) - Ordered array of actions available on the resource each defining at least one complete HTTP transaction
+ `content` (array[[Data Structure][]]) - Array of Resource's elements

    In the interim period this may contain at maximum one
    element of the `dataStructure` type - the definition of the of the Resource
    attributes.

### Action (object)
An HTTP transaction (a request-response transaction). Actions are specified by an HTTP request method within a resource.

#### Properties
+ `name` (string) - Name of the Action
+ `description` (string) - Description of the Action (`.raw` or `.html`)
+ `method` (string) - HTTP request method defining the action
+ `parameters` (array[[Parameter][]]) - Ordered array of resource's URI parameters descriptions specific to this action
+ `examples` (array[[Transaction Example][]]) - Ordered array of HTTP transaction [examples](#example-section) for the relevant HTTP request method
+ `attributes` (object) - Action-specific attributes
    + `uriTemplate` (string) - URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
    + `relation` (string) - Link relation identifier of the action as defined in [Relation section][]
+ `content` (array[[Data Structure][]])  - Array of Action's elements

    In the interim period this may contain at maximum one
    element of the `dataStructure` type - the definition of the of the action input
    attributes.

### Payload (object)
An [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

#### Properties
+ `name` (string) - Name of the payload

    The content of this key depends on the type of payload:

    + **model** payload: name of the resource, if any
    + **request** payload: name of the request, if any
    + **response** payload: HTTP status code

+ `reference` ([Reference][]) - Present if and only if a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) is present in the payload and it has been resolved correctly
+ `description` (string) - Description of the payload (`.raw` or `.html`)
+ `headers` (array[[Header][]]) - Ordered array of HTTP headers that are expected to be transferred with HTTP message represented by this payload
+ `body` (string) - **Deprecated**

    An entity body to be transferred with HTTP message represented by this payload

    Note this property is **deprecated** and will be removed in a future.
    Replaced by `bodyExample` [Asset][] element.

+ `schema` (string) - **Deprecated**

    A validation schema for the entity body as defined in `body`.

    Note this property is **deprecated** and will be removed in a future.
    Replaced by `bodySchema` `asset` element.

+ `content` (array) - Array of Payloads's elements

    In the interim period this may contain only:
    + At maximum one element of the `dataStructure` type - the definition of the message-body
    attributes
    + Up to two asset elements one for body example other for body schema

    + Items
        + ([Data Structure][])
        + ([Asset][])

### Header
A HTTP header that are expected to be transferred with an HTTP message.

#### Properties
+ `name` (string, required) - The name of the header
+ `value` (string, required) - The value of the header

### Asset (Element)
An [API Blueprint asset][]. The content is an Asset in its textual
representation as written in the source API Blueprint.

#### Properties
+ `element`: `asset` (fixed, required)
+ `attributes`
    + `role` - Role of the asset
        + `bodyExample` - Asset is an example of message-body
        + `bodySchema` - Asset is an schema for message-body

### Parameter (object)
Description of one URI template parameter.

#### Properties
- `name` (string) - Name of the parameter
- `description` (string) - Description of the parameter (`.raw` or `.html`)
- `type` (string) - An arbitrary type of the parameter (a string)
- `required` (boolean) - Boolean flag denoting whether the parameter is required (true) or not (false)
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
+ `requests` (array[[Payload][]]) - Ordered array of example transaction request payloads
+ `responses` (array[[Payload][]]) - Ordered array of example transaction response payloads

### Reference (object)
A reference object which is used whenever there is a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection).

#### Properties
+ `id` (string) - The identifier (name) of the reference

## Media Types
The `application/vnd.apiblueprint.ast` is the media type for API Blueprint AST. The media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats
Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.ast.json`
+ `application/vnd.apiblueprint.ast.yaml`

### Example: JSON Serialization
`application/vnd.apiblueprint.ast.json; version=4.0`

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
  "element": "category",
  "content": [
    {
      "element": "category",
      "attributes": {
        "name": "<resource group name>"
      },
      "content": [
        {
          "element": "copy",
          "content": "<resource group description>"
        },
        {
          "name": "<resource name>",
          "description": "<resource description>",
          "element": "resource",
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
            "content": [
              {
                "element": "dataStructure",
                "content":{
                  "element": "object",
                  "meta": {
                    "ref": "<sub-type>"
                  },
                  "content": []
                }
              },
              {
                "element": "asset",
                "attributes":  {
                  "role": "bodyExample"
                },
                "content": "<resource model body>"
              },
              {
                "element": "asset",
                "attributes":  {
                  "role": "bodySchema"
                },
                "content": "<resource model schema>"
              }
            ]
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
                      "content": [
                        {
                          "element": "dataStructure",
                          "content": {
                            "element": "object",
                            "meta": {
                              "ref": "<sub-type>"
                            },
                            "content": []
                          }
                        },
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodyExample"
                          },
                          "content": "<request body>"
                        },
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodySchema"
                          },
                          "content": "<request schema>"
                        }
                      ]
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
                      "content": [
                        {
                          "element": "dataStructure",
                          "content": {
                            "element": "object",
                            "meta": {
                              "ref": "<sub-type>"
                            },
                            "content": []
                          }
                        },
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodyExample"
                          },
                          "content": "<request body>"
                        },
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodySchema"
                          },
                          "content": "<request schema>"
                        }
                      ]
                    }
                  ]
                }
              ],
              "content": [
                {
                  "element": "dataStructure",
                  "content": {
                    "element": "object",
                    "meta": {
                      "ref": "<sub-type>"
                    },
                    "content": []
                  }
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
                      "content": [
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodyExample"
                          },
                          "content": "<resource model body>"
                        },
                        {
                          "element": "asset",
                          "attributes": {
                            "role": "bodySchema"
                          },
                          "content": "<resource model schema>"
                        }
                      ]
                    }
                  ]
                }
              ]
            }
          ],
          "content": [
            {
              "element": "dataStructure",
              "content": {
                "element": "object",
                "meta": {
                  "id": "<resource-name>",
                  "ref": "<sub-type>"
                },
                "content": []
              }
            }
          ]
        }
      ]
    },
    {
      "element": "category",
      "content": [
        {
          "element": "dataStructure",
          "content": {
            "element": "object",
            "meta": {
              "id": "<data-structure-name>",
              "ref": "<sub-type>"
            },
            "content": []
          }
        }
      ]
    }
  ]
}
```

## Related Media Types
- [**Serialized Parsing Result Media Types**][Parsing media types] - Media types for the serialization of complete parsing results (including warnings and errors)

## Serialization in Drafter
The `drafter` [command-line tool](https://github.com/apiaryio/drafter#drafter-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) via the `--format` option. Similarly, it also supports serialization of [API Blueprint Source Map](https://github.com/apiaryio/snowcrash/blob/master/src/BlueprintSourcemap.h) using the `--format` and `--sourcemap` option.

## License
MIT License. See the [LICENSE](LICENSE) file.

[Parsing media types]: Parse%20Result.md

[MSON]: https://github.com/apiaryio/mson

[API Blueprint asset]: https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#asset

[Attribute section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-attributes-section
[Resource section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-resource-section
[Relation section]: https://github.com/apiaryio/api-blueprint/blob/zdne/attributes-description/API%20Blueprint%20Specification.md#def-relation-section

[Blueprint]: #blueprint-object
[Element]: #element-object
[Resource Group]: #resource-group-object
[Resource]: #resource-object
[Action]: #action-object
[Payload]: #payload-object
[Reference]: #reference-object
[Asset]: #asset-element
[Parameter]: #parameter-object
[Transaction Example]: #transaction-example-object
[Attributes]: #attributes-data-structure
[Data Structure]: https://github.com/refractproject/refract-spec/blob/master/namespaces/api-description-namespace.md#data-structure-element
[Data Structures]: #data-structures-object

[Source Map Definition]: Source%20Map.md
