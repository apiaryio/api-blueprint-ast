![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST Serialization Media Types
### API Blueprint's contract with machines

This document defines serialization formats of [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short.

API Blueprint AST is a machine-friendly face of API Blueprint suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint.

Converting API Blueprint to AST and its serialization is the task of API Blueprint Parser – [Snow Crash](https://github.com/apiaryio/snowcrash) or one of its [bindings](https://github.com/apiaryio/snowcrash#bindings). Reverse process from AST (serialization) to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## What?

**This document is intended for authors of tools using API Blueprint**. If you are looking for a way to describe your Web API proceed directly to [API Blueprint](https://github.com/apiaryio/api-blueprint).

## Version

+ **Version**: 2.0
+ **Created**: 2013-08-30
+ **Updated**: 2014-06-09

---

## Quick Links

+ [Media Types](#media-types)
+ [JSON serialization](#json-serialization)
+ [YAML serialization](#yaml-serialization)
+ [Keys description](#keys-description)
+ [Serialization in Snow Crash](#serialization-in-snow-crash)
+ [Serialized Parsing Result Media Types][parsing media types]

---

## Media Types

The `application/vnd.apiblueprint.ast` is the base media type for API Blueprint AST. An API Blueprint AST with raw Markdown descriptions has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html` suffix. The base media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats

Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.ast.raw+json` and `application/vnd.apiblueprint.ast.html+json`
+ `application/vnd.apiblueprint.ast.raw+yaml` and `application/vnd.apiblueprint.ast.html+yaml`

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

## Keys Description

Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details.

### Blueprint Section

+ `_version` ... Version of the AST Serialization as [defined](#version) in this document
+ `metadata` ... Ordered array of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
+ `name` ... Name of the API
+ `description` ... Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` ... Ordered array of [resources groups](#resource-group-section)

### Resource Group Section

Logical group of resources.

+ `name` ... Name of the Resource Group
+ `description` ... Description of the Resource Group (`.raw` or `.html`)
+ `resources` ... Ordered array of the respective [resources](#resource-section) belonging to the Resource Group

### Resource Section

Description of one resource, or a cluster of resources defined by its URI template.

+ `name` ... Name of the Resource
+ `description` ... Description of the Resource (`.raw` or `.html`)
+ `uriTemplate` ... URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ... [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable [payload](#payload-section) representing the resource
+ `parameters` ... Ordered array of URI [parameters](#parameter-section) description sections
+ `actions` ... Ordered array of actions available on the resource each defining at least one complete HTTP transaction

### Action Section

+ `name` ... Name of the Action
+ `description` ... Description of the Action (`.raw` or `.html`)
+ `method` ... HTTP request method defining the action
+ `parameters` ... Ordered array of resource's URI [parameters](#parameter-section) description sections specific to this action
+ `examples` ... Ordered array of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Section

An [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

+ `name` ... Name of the payload

    The content of this key depends on the type of payload:

    + **model** payload: name of the resource, if any
    + **request** payload: name of the request, if any
    + **response** payload: HTTP status code

+ `description` ... Description of the payload (`.raw` or `.html`)
+ `headers` ... Ordered array of HTTP headers that are expected to be transferred with HTTP message represented by this payload
+ `body` ... An entity body to be transferred with HTTP message represented by this payload
+ `schema` ... A validation schema for the entity body as defined in `body`

### Parameter Section

Description of one URI template parameter.

- `description` ... Description of the parameter (`.raw` or `.html`)
- `type` ... An arbitrary type of the parameter (a string)
- `required` ... Boolean flag denoting whether the parameter is required (true) or not (false)
- `default` ... A default value of the parameter (a value assumed when the parameter is not specified)
- `example` ... An example value of the parameter
- `values` ... An array enumerating possible parameter values. Array item is an hash with the "value" key denoting the enumerated value.

### Example Section

An HTTP transaction example with expected HTTP message request and response payload(s).

+ `name` ... Name of the Transaction Example
+ `description` ... Description of the Transaction Example (`.raw` or `.html`)
+ `requests` ... Ordered array of example transaction request [payloads](#payload-section)
+ `requests` ... Ordered array of example transaction response [payloads](#payload-section)

## Serialization in Snow Crash

The `snowcrash` [command-line tool](https://github.com/apiaryio/snowcrash#snow-crash-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) via the `--format` option.

## Related Media Types

- [**Serialized Parsing Result Media Types**][parsing media types] - Media types for the serialization of complete parsing results (including warnigns and errors)

## License

MIT License. See the [LICENSE](LICENSE) file.


[parsing media types]: Parse%20Result.md
