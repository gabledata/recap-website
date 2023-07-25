---
layout: default
title: "JSON Schema"
parent: "Converters"
---

# JSON Schema
{: .no_toc }

1. TOC
{:toc}

Recap uses the `JSONSchemaConverter` class to convert between Recap types and JSON Schema types. `JSONSchemaConverter` has one main method: `to_recap`.

## `to_recap`

This method converts a JSON Schema to a Recap type.

```python
def to_recap(self, json_schema_str: str) -> StructType:
```

The `to_recap` method takes a JSON Schema as a string input, parses it, and transforms it into an equivalent Recap schema, which is returned. If the root of the JSON Schema is not an object, a `ValueError` is raised.

## Examples

### JSON Schema to Recap


```python
from recap.converters.json_schema import JSONSchemaConverter

# Define a JSON Schema
json_schema = """
{
    "type": "object",
    "properties": {
        "name": {
            "type": "string"
        },
        "age": {
            "type": "integer"
        }
    }
}
"""

# Convert to Recap schema
recap_schema = JSONSchemaConverter().to_recap(json_schema)
```

## Type Conversion

This table shows the corresponding Recap types for each JSON Schema type.

| JSON Schema Type | Recap Type |
|------------------|------------|
| object | StructType |
| array | ListType |
| string (date format) | StringType (with `org.iso.8601.Date` date logical type) |
| string (date-time format) | StringType (with `org.iso.8601.DateTime` datetime logical type) |
| string (time format) | StringType (with `org.iso.8601.Time` logical type) |
| string | StringType(bytes=9_223_372_036_854_775_807, variable=True) |
| number | FloatType(bits=64) |
| integer | IntType(bits=32) |
| boolean | BoolType |
| null | NullType |

## Limitations and Constraints

JSON Schema types have some restrictions in conversion to Recap types:

- The main `to_recap` function requires the root JSON schema to be an object (i.e., a `StructType` in Recap).
- Only supports the conversion of JSON Schema primitive types (null, boolean, object, array, number, string, and integer).
- Unsupported JSON Schema constructs will raise a `ValueError`.