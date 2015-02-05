---

Author: Zdenek Nemec
Version: 4.0.0

---

# API Blueprint DOM

This document defines the elements of API Blueprint document object model.
A cross-platform and language-independent convention for representing and
interacting with elements of API description.

## About this Document

This document conforms to RFC 2119, which says:

> The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”,
“SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be
interpreted as described in RFC 2119.

Additionally, [MSON][] is used throughout this document to describe and define
data structures.

## Status: Quicksand

At this point this document is just a sketch of what the API Blueprint DOM may
look like.

## Base Element

TODO: Following base element will be defined in the Refract base namespace.

## Refract:Element

- `element` (enum, required)
    - (string)
    - (Element)


- `attributes` (enum)
    - (object)
    - array[Element]


- `content` (enum)
    - (string)
    - (number)
    - (boolean)
    - (array)
    - (object)
    - (Element)
    - array[Element]

---

## Category (Refract:Element)

Category element is simply a grouping element – an array of other elements.

### Properties

- `element`: category (string, fixed)
- `attributes` (object)
    - `label` (string) - Human-readable name of the category


- `content` (array[Refract:Element])

## Copy (Refract:Element)

Copy elements represents a copy text. A textual information in API description.
Its content is a string and it MAY include attribute denoting the media type of
the copy content.

### Properties

- `element`: copy (string, fixed)
- `attributes` (object)
    - `contentType` (string) - Optional media type of the content e.g.
        `text/html`, or `text/plain`


- `content` (string)

## Data Structure (MSON:Data Structure)

Data Structure is an element that describes and defines an arbitrary data
structure. This is identical to [MSON DOM][] data structure element.

### Properties

- `element`: dataStructure (string, fixed)

## Input Property (object)

- `name` (string)
- `value` (Data Structure)

## Resource (Category)

Resource element groups elements that form an description and definition of an
API resource.

### Properties

- `element`: resource (string, fixed)
- `content` (array)
    - (Copy) - Copy element discussing the resource
    - (Data Structure) - Definition of resource data structure
    - (Transition) - Resource state transition


## Transition (Category)

Element defining a state transition.

### Properties

- `element`: transition (string, fixed)
- `attributes` (object)
    - `relation` (string) - relation type of the transition
    - `http` (HTTP Transition Attributes)


- `content` (array)
    - (Copy) - Copy element discussing the transition
    - (Data Structure) - Description of input property
    - (Transition Example) - Example of the data transaction

## HTTP Transition Attributes (object)

### Properties

- `uriTemplate` (string) - URI template matching the resource in the format
    defined by [RFC 6570][]


- `method` (string)

## Transition Example (Category)

Example of a transaction.

### Properties

- `element`: transitionExample (string, fixed)
- `attributes` (object)
    - `http` (HTTP Transition)

- `content` (array)
    - (Request Payload Example)
    - (Response Payload Example)

## Payload Example (Category)

Example of an API message payload.

### Properties

- `element`: payloadExample (string, fixed)
- `content` (array)
    - (Copy) - Description of the payload example
    - (Metadata) - Payload metadata
    - (Data Structure) - Definition of the payload data structure
    - (Asset) - Data asset associated with the payload

## Asset (Refract:Element)

Asset element represents a data asset of API description.

### Properties

- `element`: asset (string, fixed)
- `attributes` (object)
    - `contentType` (string) - Optional media type of the asset

    - `role`(string) - Role of the asset
        - bodyExample - Asset is an example of message-body
        - bodySchema - Asset is an schema for message-body


- `content` (string) - The textual representation as written in the source
    blueprint

## Metadata (Category)

Grouping element for key-value metadata pairs.

### Properties

- `element`: metadata (string, fixed)
- `content` (array)
    - (object)
        - `key` (string)
        - `value` (string)


---

[MSON]: https://github.com/apiaryio/mson
[MSON DOM]: https://github.com/apiaryio/mson-ast
[RFC 6570]: http://tools.ietf.org/html/rfc6570
