![logo](https://raw.github.com/apiaryio/api-blueprint/gh-pages/assets/logo_apiblueprint.png) 

# Parse Result Media Types
This document describes API Blueprint Serialized Parse Result Media Types – the media type used to serialize a result of parsing an API Blueprint document. In addition to parsed [API Blueprint AST](README.md) this media types bear the information on any and source annotations – warnings and errors - issued by the parser during parsing. 

## Media Types

The base media type for API Blueprint Parsing result is `vnd.apiblueprint.parseresult`. A Parser Result with raw Markdown descriptions in API Blueprint AST has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html`. 

The base media type serialization format is specified in the `+<serialization format>` appendix.

## Version

+ **Version**: 1.0
+ **Created**: 2014-06-09
+ **Updated**: 2014-06-09
+ **AST Serialization Media Types**: 2.0

---

## Quick Links

+ [Media Types](#media-types)
+ [JSON serialization](#json-serialization)
+ [YAML serialization](#yaml-serialization)
+ [Keys description](#keys-description)

---

### Serialization formats

Two supported, feature-equal serialization formats are JSON and YAML:

+ `vnd.apiblueprint.parseresult.raw+json` and `vnd.apiblueprint.parseresult.html+json`
+ `vnd.apiblueprint.parseresult.raw+yaml` and `vnd.apiblueprint.parseresult.html+yaml`

Parser Result Media Types inherit from the respective [AST Serialization Type](README.md): 

+ [`vnd.apiblueprint.ast.*+json`](#json-serialization)
+ [`vnd.apiblueprint.ast.*+yaml`](#yaml-serialization)

### JSON Serialization

`vnd.apiblueprint.parseresult.*+json; version=1.0`

```json
{
  "_version": "1.0",
  "ast": {
    "_version": "2.0",

    ...

  },
  "error": {
    "code": <error code>,
    "message": "<error message>",
    "location": [
      {
        "index": <character index>,
        "length": <character count>
      }
    ]
  },
  "warnings": [
    {
      "code": <warning code>,
      "message": "<warning message>",
      "location": [
        {
          "index": <character index>,
          "length": <character count>
        }
      ]
    }
  ]
}
```

### YAML Serialization

`vnd.apiblueprint.parseresult.*+yaml; version=1.0`

```yaml
_version: "1.0"
ast: 
  _version: "2.0"

  ...

error:
  code: <error code>
  message: "<error message>"
  location: 
    - index: <character index>
      length: <character count>

warnings: 
  - code: <warning code>
    message: "<warning message>"
    location: 
      - index: <character index>
        length: <character count>
```

## Keys Description

### Top-level keys
- `_version` ... Version of the Parse Result Media Type as [defined](#version) in this document.
- `ast` ... This is the abstract syntax tree (AST) of the parsed blueprint. See [`ast`](#ast).
- `error` ... Parsing error, if any. See [`error`](#error).
- `warnings` ... Ordered array of warnings occurred during the parsing, if any. See [`warnings`](#warnings).

#### `ast`

The structure under this key is defined by the [AST Blueprint serialization Media Type v2.0](https://github.com/apiaryio/api-blueprint-ast#json-serialization) – `vnd.apiblueprint.ast.raw+json; version=2.0`.

#### `error` 

Description of a parsing error as occurred during parsing. If this field is present and `code` different from `0` then the content of `ast` field should be ignored.

+ `code` ... Error code. The `0` means success – no error occurred.
+ `message` ... Error message.
+ `location` ... Error source map  - see [`location`](#location).

#### `warnings`

Ordered array of parser warnings as occurred during the parsing.

+ `code` ...  Warning [group](https://github.com/apiaryio/snowcrash/blob/master/src/SourceAnnotation.h#L128) code.
+ `message` ... Warning message.
+ `location` ... Warning source map  - see [`location`](#location).

#### `location`
 
Array of possibly non-continuous blocks of the source API Blueprint.

+ `index` ... Zero-based index of the character where warning has occurred.
+ `length` ... Number of the characters from index where warning has occurred.

