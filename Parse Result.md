![logo](https://raw.github.com/apiaryio/api-blueprint/master/assets/logo_apiblueprint.png)

# Parse Result Media Types
This document describes API Blueprint Serialized Parse Result Media Types – the media type used to serialize a result of parsing an API Blueprint document. In addition to parsed [API Blueprint AST](README.md) this media types bear the information on any and source annotations – warnings and errors - issued by the parser during parsing.

## Version

+ **Version**: 1.0
+ **Created**: 2014-06-09
+ **Updated**: 2014-08-14
+ **AST Serialization Media Types**: 2.0

---

## Quick Links

+ [Parse Result Description](#parse-result-description)
+ [Media Types](#media-types)
+ [JSON serialization](#json-serialization)
+ [YAML serialization](#yaml-serialization)
+ [Keys description](#keys-description)

---

## Parse Result Description
Following is the descripton of Parse Result media types using the [MSON](https://github.com/apiaryio/mson) syntax.

### Parse Result Object

- `_version` (string) - Version of the Parse Result Media Type as [defined](#version) in this document
- `ast` ([AST](#ast-object)) - This is the abstract syntax tree (AST) of the parsed blueprint
- `error` ([Error](#error-object)) - Parsing error, if any
- `warnings` (array: [Warning](#warning-object)) - Ordered array of warnings occurred during the parsing, if any

### AST Object
This object is the [Blueprint](README.md#blueprint-object) object as defined in the [AST Blueprint serialization Media Type v2.0](https://github.com/apiaryio/api-blueprint-ast).

### Error Object
Description of a parsing error as occurred during parsing. If this field is present and `code` different from `0` then the content of `ast` field should be ignored.

#### Attributes

+ `code` (number) - Error code. The `0` means success – no error occurred
+ `message` (string) - Error message
+ `location` (array: [Location](#location-object)) – Error source map

### Warning Object

+ `code` (number) - Warning [group code](https://github.com/apiaryio/snowcrash/blob/master/src/SourceAnnotation.h#L128)
+ `message` (string) - Warning message
+ `location` (array: [Location](#location-object)) – Warning source map

### Location Object

+ `index` (number) - Zero-based index of the character where warning has occurred
+ `length` (number) - Number of the characters from index where warning has occurred

---

## Media Types

The base media type for API Blueprint Parsing result is `application/vnd.apiblueprint.parseresult`. A Parse Result with raw Markdown descriptions in API Blueprint AST has the `.raw` suffix whereas version with Markdown descriptions rendered into HTML has the `.html`.

The base media type serialization format is specified in the `+<serialization format>` appendix.

### Serialization formats

Two supported, feature-equal serialization formats are JSON and YAML:

+ `application/vnd.apiblueprint.parseresult.raw+json` and `application/vnd.apiblueprint.parseresult.html+json`
+ `application/vnd.apiblueprint.parseresult.raw+yaml` and `application/vnd.apiblueprint.parseresult.html+yaml`

Parser Result Media Types inherit from the respective [AST Serialization Type](README.md):

+ [`application/vnd.apiblueprint.ast.*+json`](#json-serialization)
+ [`application/vnd.apiblueprint.ast.*+yaml`](#yaml-serialization)

### JSON Serialization

`application/vnd.apiblueprint.parseresult.*+json; version=1.0`

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

`application/vnd.apiblueprint.parseresult.*+yaml; version=1.0`

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

