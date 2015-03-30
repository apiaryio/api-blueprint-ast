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
Following is the definition of Source map media types using the [MSON][] syntax.

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
+ `metadata` (array[[Source Map][]]) - An array of source maps where each item in metadata has its own source map
+ `name` ([Source Map][]) - Source map of API name
+ `description` ([Source Map][]) - Source map of API description
+ `resourceGroups` (array[[Resource Group Source Map][]]) - **Deprecated**
+ `content` (array[[Element Source Map][]]) - Source map of section elements

### Element Source Map (object)
+ `attributes`
    + `name` ([Source Map][]) - Source map of the element name
+ `content` (enum)
    + (array[[Element Source Map][]]) - Source maps of nested elements (for element `category`)
    + ([Source Map][]) - Source map (for element `copy` and `asset`)
    + ([Resource Source Map][]) - Source map of Resource (for element `resource`)
    + ([Data Structure Source Map][]) - Source map of Data Structure (for element `dataStructure`)

### Resource Group Source Map (object)
**Deprecated**

Source map of the [Resource Group][].

#### Properties
+ `name` ([Source Map][]) - Source map of name of the Resource Group
+ `description` ([Source Map][]) - Source map of description of the Resource Group
+ `resources` (array[[Resource Source Map][]]) - Ordered array of the respective resources belonging to the Resource Group

### Resource Source Map (object)
Source map of the [Resource][].

#### Properties
+ `name` ([Source Map][]) - Source map of name of the Resource
+ `description` ([Source Map][]) - Source map of description of the Resource
+ `uriTemplate` ([Source Map][]) - Source map of URI Template
+ `model` ([Payload Source map][]) - [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection), a reusable payload representing the resource
+ `parameters` (array[[Parameter Source Map][]]) - Ordered array of source maps of URI parameters
+ `actions` (array[[Action Source Map][]]) - Ordered array of source maps of actions available on the resource each defining at least one complete HTTP transaction
+ `content` (array[[Data Structure Source Map][]]) - Ordered array of Resource source map's elements

### Action Source Map (object)
Source map of the [Action][].

#### Properties
+ `name` ([Source Map][]) - Source map of name of the Action
+ `description` ([Source Map][]) - Source map of description of the Action
+ `method` ([Source Map][]) - Source map of HTTP request method defining the action
+ `parameters` (array[[Parameter Source Map][]]) - Ordered array of source maps of resource's URI parameters descriptions specific to this action
+ `examples` (array[[Transaction Example Source Map][]]) - Ordered array of source maps of HTTP transaction [examples](#example-section) for the relevant HTTP request method
+ `attributes` (object)
    + `uriTemplate` (string) - Source map of URI Template
    + `relation` (string) - Source map of link relation identifier of the action
+ `content` (array[[Data Structure Source Map][]]) - Ordered array of Action source map's elements

### Payload Source Map (object)
Source map of [Payload][].

When a reference is used, the source map of the payload is in fact the source map of the [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection).

#### Properties
+ `name` ([Source Map][]) - Source map of name of the payload
+ `reference` ([Source Map][]) - Source map of the reference, present if and only if a reference to a [Resource Model](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md#ResourceModelSection) is present in the payload and it has been resolved correctly
+ `description` ([Source Map][]) - Source map of description of the payload
+ `attributes` ([Attributes Source Map][]) - Source map of attributes of the Payload
+ `headers` (array[[Source Map][]]) - Ordered array of source maps of HTTP headers that are expected to be transferred with HTTP message represented by this payload. Each item in the header has it's own source map.
+ `body` ([Source Map][]) - **Deprecated**

    Source map of body to be transferred with HTTP message represented by this payload

    Note this property is **deprecated** and will be removed in a future. Use `assets/body/source` instead.

+ `schema` ([Source Map][]) - **Deprecated**

    Source map of a validation schema for the entity body as defined in `body`.

    Note this property is **deprecated** and will be removed in a future. Use `assets/schema/source` instead.

+ `content` (array) - Ordered array of Payload source map's elements

    + Items
        + ([Data Structure Source Map][])
        + ([Asset Source Map][])

### Asset Source Map ([Element Source Map][])
Source map of [Asset][]

### Parameter Source Map (object)
Source map of [Parameter][].

#### Properties
+ `description` ([Source Map][]) - Source map of description of the parameter
+ `type` ([Source Map][]) - Source map of an arbitrary type of the parameter (a string)
+ `required` ([Source Map][]) - Source map of boolean flag denoting whether the parameter is required (true) or not (false)
+ `default` ([Source Map][]) - Source map of a default value of the parameter (a value assumed when the parameter is not specified)
+ `example` ([Source Map][]) - Source map of an example value of the parameter
+ `values` (array[[Source Map][]]) - Source map of an array enumerating possible parameter values. Each item has it's own source map.

### Transaction Example Source Map (object)
Source map of [Transaction Example][].

#### Properties
+ `name` ([Source Map][]) - Source map of name of the Transaction Example
+ `description` ([Source Map][]) - Source map of description of the Transaction Example
+ `requests` (array[[Payload Source Map][]]) - Ordered array of source maps of example transaction request payloads
+ `responses` (array[[Payload Source Map][]]) - Ordered array of source maps of example transaction response payloads

### Data Structure Source Map ([Named Type Source Map][])
Source map of [Data Structure][]

## Media Types
The `application/vnd.apiblueprint.sourcemap` is the base media type for API Blueprint AST Source Map.

### Serialization formats
Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.sourcemap+json`
+ `application/vnd.apiblueprint.sourcemap+yaml`


[MSON]: https://github.com/apiaryio/mson
[API Blueprint AST Definition]: README.md

[Blueprint]: README.md#blueprint-object
[Resource Group]: README.md#resource-group-object
[Resource]: README.md#resource-object
[Action]: README.md#action-object
[Payload]: README.md#payload-object
[Asset]: README.md#asset-object
[Parameter]: README.md#parameter-object
[Transaction Example]: README.md#transaction-example-object
[Attributes]: README.md#attributes-data-structure
[Data Structure]: README.md#data-structure-object
[Data Structures]: README.md#data-structures-object

[Source Map]: #source-map-array
[Blueprint Source Map]: #blueprint-source-map-object
[Resource Group Source Map]: #resource-group-source-map-object
[Resource Source Map]: #resource-source-map-object
[Action Source Map]: #action-source-map-object
[Payload Source Map]: #payload-source-map-object
[Asset Source Map]: #asset-source-map-element-source-map
[Parameter Source Map]: #parameter-source-map-object
[Transaction Example Source Map]: #transaction-example-source-map-object
[Data Structure Source Map]: #data-structure-source-map-named-type-source-map
[Element Source Map]: #element-source-map-object

[Named Type Source Map]: https://github.com/apiaryio/mson-ast/blob/master/Source%20Map.md#named-type-source-map-object
