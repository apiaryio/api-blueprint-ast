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

This documents uses [Refract][] and its [MSON Namespace][] and [Representor Namespace][]
to define API (description) elements.

Additionally, [MSON][] is used throughout this document to describe and define
data structures.

## Status: Quicksand

At this point this document is just a sketch of what the API Blueprint DOM may
look like.

## Category (Element)

Category element is a grouping element – an array of other elements.

### Properties

- `element`: category (string, fixed)
- `meta`
    - `class` (array)
        - (enum)
            - api
            - resourceGroup
            - dataStructures
            - scenario
            - anythingElse

- `content` (array[Element])

## HTTP Transaction (Element)

Example of an HTTP Transaction. Exercising a transition initiates HTTP transaction.

### Properties

- `element`: httpTransaction (string, fixed)
- `content` (array[HTTP Message])

## HTTP Message (Element)

Data structure for describing an [HTTP Message][].

### Properties

- `element`: httpMessage (string, fixed)
- `meta`
    `class` (array, fixed)
        - (enum[string])
            - request
            - response
- `attributes`
    - `method` (string) - HTTP request method for message of `request` class
    - `uri` (string) - URI for message of `request` class
    - `headers` (Object Element)
    - `statusCode` (number) - HTTP status code for message of `response` class
    - `assets` (array[Asset]) - Array of assets associated with message description
- `content` (MSON Element)

## Asset (Element)

Asset element represents a data asset of API description.

### Properties

- `element`: asset (string, fixed)
- `meta`
    `class` (array, fixed)
        - (enum[string])
            - body - Asset is an example of message-body
            - bodySchema - Asset is an schema for message-body
- `attributes` (object)
    - `contentType` (string) - Optional media type of the asset
    - `href` - Link to the asset
- `content` (string) - A textual representation of the asset


# Example

Given the following blueprint:

```markdown
# Polls API
Polls is a simple API allowing consumers to view polls and vote in them.

## Group Question

Resources related to questions in the API.

## Question [/questions/{question_id}]

+ Parameters
    + question_id (required, number, `1`) ... ID of the Question in form of an integer

+ Attributes
    + question: `Favourite programming language?` (string, required)
    + published_at: `2014-11-11T08:40:51.620Z` (string) - An ISO8601 date when the question was published
    + choices (array[Choice], required) - An array of Choice objects
    + url: /questions/1 (string)

### View a Questions Detail [GET]

+ Response 200 (application/json)
    + Attributes (Question)
```

The API element tree is:


```json
[
    "category",
    {
        "title": "Polls API",
        "description": "Polls is a simple API allowing consumers to view polls and vote in them.",
        "class": [
            "api"
        ]
    },
    {},
    [
        "category",
        {
            "title": "Question",
            "description": "Resources related to questions in the API.",
            "class": [
                "resourceGroup"
            ]
        },
        {},
        [
            "resource",
            {
                "title": "Question"
            },
            {
                "tranistions": [
                    [
                        "tranistion",
                        {
                            "title": "View a Questions Detail"
                        },
                        {
                            "href": "/questions/{question_id}",
                            "httpTransactions": [
                                [
                                    "httpTransaction",
                                    {},
                                    {},
                                    [
                                        [
                                            "httpMessage",
                                            {
                                                "meta": [
                                                    "request"
                                                ]
                                            },
                                            {
                                                "method": "GET",
                                                "uri": "/questions/{question_id}"
                                            }
                                        ],
                                        [
                                            "httpMessage",
                                            {
                                                "meta": [
                                                    "response"
                                                ]
                                            },
                                            {
                                                "status": "200",
                                                "headers": [
                                                    "object",
                                                    {},
                                                    {},
                                                    null
                                                ]
                                            },
                                            [
                                                "object",
                                                {},
                                                {},
                                                null
                                            ]
                                        ]
                                    ]
                                ]
                            ]
                        },
                        null
                    ]
                ]
            },
            [
                "object",
                {},
                {},
                null
            ]
        ]
    ]
]
```


---

[MSON]: https://github.com/apiaryio/mson
[MSON DOM]: https://github.com/apiaryio/mson-ast
[RFC 6570]: http://tools.ietf.org/html/rfc6570

[Refract]: https://github.com/refractproject/refract-spec/blob/master/refract-spec.md
[MSON Namespace]: https://github.com/refractproject/refract-spec/blob/master/namespaces/mson-namespace.md
[Representor Namespace]: https://github.com/refractproject/refract-spec/pull/8/files#diff-c294a388538c5ba0d834d163434544d8
[HTTP Message]: http://datatracker.ietf.org/doc/rfc7230/?include_text=1
