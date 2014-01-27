![logo](https://raw.github.com/apiaryio/api-blueprint/gh-pages/assets/logo_apiblueprint.png) 

# API Blueprint AST Serialization Media Types
### API design for machines

This document defines serialization formats of [API Blueprint](http://apiblueprint.org) abstract syntax tree, or AST for short. 

API Blueprint AST is a machine-friendly face of [API Blueprint](http://apiblueprint.org) suitable for use in [tools](http://apiblueprint.org/#tooling) consuming (or producing) API Blueprint. API Blueprint AST is a product of the API Blueprint Parser – [Snow Crash](https://github.com/apiaryio/snowcrash) or one of its [bindings](https://github.com/apiaryio/snowcrash#bindings).

Reverse process from API Blueprint AST to API Blueprint is also possible thanks to the [Matter Compiler](https://github.com/apiaryio/matter_compiler).

## What?

**This document is intended for authors of tools using API Blueprint**. If you are looking for a way to describe your Web API proceed directly to [API Blueprint](https://github.com/apiaryio/api-blueprint). 

## Version

+ **Version**: 1.0
+ **Created**: 2013-08-30
+ **Updated**: 2014-01-27

---

## Quick Links

+ [Media Types](#media-types)
+ [JSON serialization](#json-serialization)
+ [YAML serialization](#yaml-serialization)
+ [Keys description](#keys-description)
+ [Serialization in Snow Crash](#serialization-in-snow-crash)

---

## Media Types

The `vnd.apiblueprint.ast` is the base media type for API Blueprint AST. An API Blueprint AST with raw Markdown descriptions has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html` suffix. 

### Serialization formats

+ **JSON**-based: `vnd.apiblueprint.ast.raw+json; version=1.0`
+ **YAML**-based: `vnd.apiblueprint.ast.raw+yaml; version=1.0`

### JSON Serialization

`vnd.apiblueprint.ast.raw+json; version=1.0`

```json
{
  "_version": "<AST version>",
  "metadata": {
    "<key>": {
      "value": "<value>"
    }
  },
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
            "headers": {
              "<header>": {
                "value": "<header value>"
              }
            },
            "body": "<resource model body>",
            "schema": "<resource model schema>"
          },
          "parameters": {
            "<parameter>": {
              "description": "<description>",
              "type": "<type>",
              "required": "<required parameter flag>",
              "default": "<default value>",
              "example": "<example value>",
              "values": [
                "<element>"
              ]
            }
          },
          "headers": {
            "<header>": {
              "value": "<header value>"
            }
          },
          "actions": [
            {
              "name": "<action name>",
              "description": "<action description>",
              "method": "<action HTTP request method>",
              "parameters": {
                "<parameter>": {
                  "description": "<description>",
                  "type": "<type>",
                  "required": "<required parameter flag>",
                  "default": "<default value>",
                  "example": "<example value>",
                  "values": [
                    "<element>"
                  ]
                }
              },
              "headers": {
                "<header>": {
                  "value": "<header value>"
                }
              },
              "examples": [
                {
                  "name": "<transaction example name>",
                  "description": "<transaction example name>",
                  "requests": [
                    {
                      "name": "<request name>",
                      "description": "<request description>",
                      "headers": {
                        "<header>": {
                          "value": "<header value>"
                        }
                      },
                      "body": "<request body>",
                      "schema": "<request schema>"
                    }
                  ],
                  "responses": [
                    {
                      "name": "<response HTTP status code>",
                      "description": "<response description>",
                      "headers": {
                        "<header>": {
                          "value": "<header value>"
                        }
                      },
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

`vnd.apiblueprint.ast.raw+yaml; version=1.0`

```yaml
_version: <AST version>

metadata:
  <key>:
    value: <value>

name: <API name>
description: <API description>

resourceGroups:
- name: <resource group name>
  description: <resource group description>

  resources:
  - name: <resource name>
    description: <resource description>
    uriTemplate: <resource URI template>

    model:
      name: <resource model name>
      description: <resource model description>

      headers:
        <header>:
          value: <header value>

      body: <resource model body>
      schema: <resource model schema>

    parameters:
      <parameter>:
        description: <description>
        type: <type>
        required: <required parameter flag>
        default: <default value>
        example: <example value>
        values:
          - <element>

    headers:
      <header>:
        value: <header value>

    actions:
    - name: <action name>
      description: <action description>
      method: <action HTTP request method>

      parameters:
        <parameter>:
          description: <description>
          type: <type>
          required: <required parameter flag>
          default: <default value>
          example: <example value>
          values:
            - <element>      

      headers:
        <header>:
          value: <header value>

      examples:
      - name: <transaction example name>
        description: <transaction example name>

        requests:
        - name: <request name>
          description: <request description>

          headers:
            <header>:
              value: <header value>

          body: <request body>
          schema: <request schema>

        responses:
        - name: <response HTTP status code>
          description: <response description>

          headers:
            <header>:
              value: <header value>

          body: <response body>
          schema: <response schema>
```

## Keys Description

Most of the keys corresponds to their counter parts in API Blueprint Specification. Where applicable, refer to the relevant entry [API Blueprint Language Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md) for details. 

### Blueprint Section 

+ `_version` ... Version of the AST Serialization as [defined](#version) in this document
+ `metadata` ... A hash of API Blueprint [metadata](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#MetadataSection)
+ `name` ... Name of the API
+ `description` ... Top-level description of the API in Markdown (`.raw`) or HTML (`.html`)
+ `resourceGroups` ... Array of [resources groups](#resource-group-section)

### Resource Group Section

Logical group of resources.

+ `name` ... Name of the Resource Group
+ `description` ... Description of the Resource Group (`.raw` or `.html`)
+ `resources` ... An array of the respective [resources](#resource-section) belonging to the Resource Group

### Resource Section

Description of one resource, or a cluster of resources defined by its URI template.

+ `name` ... Name of the Resource
+ `description` ... Description of the Resource (`.raw` or `.html`)
+ `uriTemplate` ... URI Template as defined in [RFC6570](http://tools.ietf.org/html/rfc6570)
+ `model` ... [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable [payload](#payload-section) representing the resource
+ `parameters` ... Hash of URI [parameters](#parameter-section) description sections
+ `headers` ... A hash of HTTP headers that are expected to be send and received with every HTTP message bound to this resource
+ `actions` ... An array of actions available on the resource each defining at least one complete HTTP transaction

### Action Section 

+ `name` ... Name of the Action
+ `description` ... Description of the Action (`.raw` or `.html`)
+ `method` ... HTTP request method defining the action
+ `parameters` ... Hash of parent resource's URI [parameters](#parameter-section) description sections specific to this action
+ `headers` ... A hash of HTTP headers that are expected to be send and received with every HTTP message bound to this resource with the same HTTP request method
+ `examples` ... An array of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Section

An [API Blueprint payload](https://github.com/apiaryio/api-blueprint/blob/master/Glossary%20of%20Terms.md#payload).

+ `name` ... Name of the payload 

    The content of this key depends on the type of payload:

    + **model** payload: name of the resource, if any
    + **request** payload: name of the request, if any 
    + **response** payload: HTTP status code

+ `description` ... Description of the payload (`.raw` or `.html`)
+ `headers` ... A hash of HTTP headers that are expected to be transferred with HTTP message represented by this payload
+ `body` ... An entity body to be transferred with HTTP message represented by this payload
+ `schema` ... A validation schema for the entity body as defined in `body`

### Parameter Section

Description of one URI template parameter.

- `description` ... Description of the parameter (`.raw` or `.html`)
- `type` ... An arbitrary type of the parameter (a string)
- `required` ... Boolean flag denoting whether the parameter is required (true) or not (false)
- `default` ... A default value of the parameter (a value assumed when the parameter is not specified)
- `example` ... An example value of the parameter
- `values` ... An array enumerating possible parameter values

### Example Section

An HTTP transaction example with expected HTTP message request and response payload(s).

+ `name` ... Name of the Transaction Example
+ `description` ... Description of the Transaction Example (`.raw` or `.html`)
+ `request` ... An array of example transaction request [payloads](#payload-section)
+ `request` ... An array of example transaction response [payloads](#payload-section)

## Serialization in Snow Crash

The `snowcrash` [command-line tool](https://github.com/apiaryio/snowcrash#snow-crash-command-line-tool) supports serialization of [API Blueprint AST](https://github.com/apiaryio/snowcrash/blob/master/src/Blueprint.h) via the `--format` option.

## License

MIT License. See the [LICENSE](LICENSE) file.
