![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# API Blueprint AST Source Map
This document defines API Blueprint AST Source Map. API Blueprint Source Map is 1-on-1 with API Blueprint AST and represents the source maps for each of the nodes in the AST tree.

## Version
Refer to the [API Blueprint AST Definition][] – the version of the Source Map always conforms to the version of API Blueprint AST.

## Quick Links
+ [Source Map Definition](#source-map-definition)
+ [Media Types](#media-types)
+ [Example: JSON serialization](#example-json-serialization)

## Source Map Definition
Following is the definition of Source map media types using the [MSON](https://github.com/apiaryio/mson) syntax.

### Source Map (array)
Set of indices to source code blocks. The blocks may be non-continuous.

Each block is defined by a zero-based index of its first character and the number of following characters.

#### Items
- (array, fixed) - A tuple defining position of a block in the source code
    - *1219* (number) - Zero-based index of the first character
    - *30* (number) - Number of the characters in the block

### Blueprint Source Map (object)
Source map of the [Blueprint][].

#### Properties
+ `metadata` (array[[Source Map](#source-map)]) - An array of source maps where each item in metadata has its own source map
+ `name` ([Source Map](#source-map)) - Source map of API name
+ `description` ([Source Map](#source-map)) - Source map of API description
+ `resourceGroups` (array[[Resource Group Source Map](#resource-group-source-map)])

### Resource Group Source Map (object)
Source map of the [Resource Group][].

#### Properties
+ `name` ([Source Map](#source-map)) - Source map of name of the Resource Group
+ `description` ([Source Map](#source-map)) - Source map of description of the Resource Group
+ `resources` (array[[Resource Source Map](#resource-source-map)]) - Ordered array of the respective resources belonging to the Resource Group

### Resource Source Map (object)
Source map of the [Resource][].

#### Properties
+ `name` ([Source Map](#source-map)) - Source map of name of the Resource
+ `description` ([Source Map](#source-map)) - Source map of description of the Resource
+ `uriTemplate` ([Source Map](#source-map)) - Source map of URI Template
+ `model` ([Payload Source map](#payload-source-map)) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter Source Map](#parameter-source-map)]) - Ordered array of source maps of URI parameters
+ `actions` (array[[Action Source Map](#action-source-map)]) - Ordered array of source maps of actions available on the resource each defining at least one complete HTTP transaction

### Action Source Map (object)
Source map of the [Action][].

#### Properties
+ `name` ([Source Map](#source-map)) - Source map of name of the Action
+ `description` ([Source Map](#source-map)) - Source map of description of the Action
+ `method` ([Source Map](#source-map)) - Source map of HTTP request method defining the action
+ `parameters` (array[[Parameter Source Map](#parameter-source-map)]) - Ordered array of source maps of resource's URI parameters descriptions specific to this action
+ `examples` (array[[Transaction Example Source Map](#transaction-example-source-map)]) - Ordered array of source maps of HTTP transaction [examples](#example-section) for the relevant HTTP request method

### Payload Source Map (object)
Source map of [Payload][].

When a reference is used, the source map of the payload is in fact the source map of the [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection).

#### Properties
+ `name` ([Source Map](#source-map)) - Source map of name of the payload
+ `reference` ([Source Map](#source-map)) - Source map of the reference, present if and only if a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) is present in the payload and it has been resolved correctly
+ `description` ([Source Map](#source-map)) - Source map of description of the payload
+ `headers` (array[[Source Map](#source-map)]) - Ordered array of source maps of HTTP headers that are expected to be transferred with HTTP message represented by this payload. Each item in the header has it's own source map.
+ `body` ([Source Map](#source-map)) - Source map of body to be transferred with HTTP message represented by this payload
+ `schema` ([Source Map](#source-map)) - Source map of a validation schema for the entity body as defined in `body`

### Parameter Source Map (object)
Source map of [Parameter][].

#### Properties
- `description` ([Source Map](#source-map)) - Source map of description of the parameter
- `type` ([Source Map](#source-map)) - Source map of an arbitrary type of the parameter (a string)
- `required` ([Source Map](#source-map)) - Source map of boolean flag denoting whether the parameter is required (true) or not (false)
- `default` ([Source Map](#source-map)) - Source map of a default value of the parameter (a value assumed when the parameter is not specified)
- `example` ([Source Map](#source-map)) - Source map of an example value of the parameter
- `values` (array[[Source Map](#source-map)]) - Source map of an array enumerating possible parameter values. Each item has it's own source map.

### Transaction Example Source Map (object)
Source map of [Transaction Example][].

#### Properties
+ `name` ([Source Map](#source-map)) - Source map of name of the Transaction Example
+ `description` ([Source Map](#source-map)) - Source map of description of the Transaction Example
+ `requests` (array[[Payload Source Map](#payload-source-map)]) - Ordered array of source maps of example transaction request payloads
+ `responses` (array[[Payload Source Map](#payload-source-map)]) - Ordered array of source maps of example transaction response payloads

## Media Types
The `application/vnd.apiblueprint.sourcemap` is the base media type for API Blueprint AST Source Map.

### Serialization formats
Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.sourcemap+json`
+ `application/vnd.apiblueprint.sourcemap+yaml`

### Example: JSON Serialization
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


[API Blueprint AST Definition]: README.md

[Blueprint]: README.md#blueprint
[Resource Group]: README.md#resource-group
[Resource]: README.md#resource
[Action]: README.md#action
[Payload]: README.md#payload
[Parameter]: README.md#parameter
[Transaction Example]: README.md#transaction-example
