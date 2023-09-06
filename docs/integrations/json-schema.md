---
layout: default
title: "JSON Schema"
parent: "Integrations"
---

# JSON Schema
{: .no_toc }

1. TOC
{:toc}

Recap uses the `JSONSchemaConverter` class to convert between Recap types and JSON Schema types. `JSONSchemaConverter` has two main methods: `to_recap` and `from_recap`.

## `from_recap`

This method converts a Recap type to a JSON Schema.

```python
def from_recap(self, recap_type: RecapType) -> dict[str, Any]:
```

The `from_recap` method takes a Recap type as input and recursively converts it into an equivalent JSON Schema. The returned JSON schema will have a `"$defs"` key containing any aliased types.

## `to_recap`

This method converts a JSON Schema to a Recap type.

```python
def to_recap(self, json_schema_str: str) -> StructType:
```

The `to_recap` method takes a JSON Schema as a string input, parses it, and transforms it into an equivalent Recap schema, which is returned. If the root of the JSON Schema is not an object, a `ValueError` is raised.

## Examples

### Recap to JSON Schema

```python
from recap.converters.json_schema import JSONSchemaConverter
from recap.types import StructType, StringType, IntType

# Define a Recap schema
recap_schema = StructType(
    fields=[
        StringType(name="name"),
        IntType(name="age", bits=32),
    ]
)

# Convert to JSON Schema
json_schema = JSONSchemaConverter().from_recap(recap_schema)
```

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

### From Recap to JSON Schema

This table shows the corresponding JSON Schema types for each Recap type.

| Recap Type | JSON Schema Type |
|------------------------------------|------------------|
| NullType                           | null |
| BoolType                           | boolean |
| IntType                            | integer |
| FloatType                          | number |
| StringType                         | string |
| BytesType                          | string (format: byte) |
| ListType                           | array |
| MapType                            | object (with "additionalProperties") |
| UnionType                          | oneOf |
| StructType                         | object |
| EnumType                           | string (with "enum" constraint) |

### From JSON Schema to Recap

This table shows the corresponding Recap types for each JSON Schema type.

| JSON Schema Type | Recap Type |
|------------------|------------|
| null | NullType |
| boolean | BoolType |
| integer | IntType(bits=32) |
| number | FloatType(bits=64) |
| string (date format) | StringType (with `org.iso.8601.Date` date logical type) |
| string (date-time format) | StringType (with `org.iso.8601.DateTime` datetime logical type) |
| string (time format) | StringType (with `org.iso.8601.Time` logical type) |
| string | StringType(bytes=9_223_372_036_854_775_807, variable=True) |
| string (byte format) | BytesType |
| array | ListType |
| object (with "additionalProperties") | MapType |
| object | StructType |
| string (with "enum" constraint) | EnumType |

## Limitations and Constraints

JSON Schema types have some restrictions in conversion to Recap types:

- The main `to_recap` function requires the root JSON schema to be an object (i.e., a `StructType` in Recap).
- Only supports the conversion of JSON Schema primitive types (null, boolean, object, array, number, string, and integer).
- Unsupported JSON Schema constructs will raise a `ValueError`.
- Logical types are ignored in from_recap.
- Many more complex constraints and JSON schema features (like anchors, dynamicAnchors, and so on) are not supported.