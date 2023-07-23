---
layout: default
title: "Avro"
---

# Avro
{: .no_toc }

1. TOC
{:toc}

Recap uses the `AvroConverter` class to convert between Recap types and Avro types. `AvroConverter` has two methods: `from_recap` and `to_recap`.

## `from_recap`

```python
def from_recap(self, recap_schema: RecapType) -> dict:
```

Converts a Recap type to an Avro schema. The method takes a RecapType object as an argument and returns an Avro schema in dictionary form.

## `to_recap`

```python
def to_recap(
    self,
    avro_schema_str: str,
    namespace: str | None = None,
) -> StructType:
```

Converts an Avro schema to a Recap schema. The method takes an Avro schema (in the form of a JSON string) and an optional namespace string as arguments, and returns a RecapType object.

## Examples

### Recap to Avro

```python
from recap.converters.avro import AvroConverter
from recap.types import StructType, StringType, IntType

# Define a Recap schema
recap_schema = StructType(fields=[
    StringType(name="name"),
    IntType(name="age", bits=32),
])

# Convert to Avro schema
avro_schema = AvroConverter().from_recap(recap_schema)
```

### Avro to Recap

```python
from recap.converters.avro import AvroConverter

# Define an Avro schema
avro_schema = """
{
    "type": "record",
    "fields": [
        {
            "name": "name",
            "type": "string"
        },
        {
            "name": "age",
            "type": "int"
        }
    ]
}
"""

# Convert to Recap schema
recap_schema = AvroConverter().to_recap(avro_schema)
```

## Type Conversion

### From Recap to Avro

| Recap Type (with attribute limits) | Avro Type |
|------------------------------------|-----------|
| NullType                           | null      |
| BoolType                           | boolean   |
| IntType (bits <= 32, signed=False or bits <= 64, signed=True) | int, long |
| FloatType (bits=32)                | float     |
| FloatType (bits=64)                | double    |
| BytesType (bytes <= 9_223_372_036_854_775_807) | bytes, fixed |
| StringType (bytes <= 9_223_372_036_854_775_807) | string    |
| StructType                         | record    |
| EnumType                           | enum      |
| ListType                           | array     |
| MapType                            | map       |
| UnionType                          | union     |
| ProxyType                          | Named types (fixed, record, enum) |

### From Avro to Recap

| Avro Type | Recap Type (with attribute limits) |
|-----------|------------------------------------|
| null      | NullType                           |
| boolean   | BoolType                           |
| int       | IntType (bits=32, signed=True)     |
| long      | IntType (bits=64, signed=True)     |
| float     | FloatType (bits=32)                |
| double    | FloatType (bits=64)                |
| bytes     | BytesType (bytes <= 9_223_372_036_854_775_807) |
| fixed (size <= 9_223_372_036_854_775_807) | BytesType (bytes=size) |
| string    | StringType (bytes <= 9_223_372_036_854_775_807) |
| record    | StructType                         |
| enum      | EnumType                           |
| array     | ListType                           |
| map       | MapType                            |
| union     | UnionType                          |
| Named types (fixed, record, enum) | ProxyType |

## Aliases

In the `from_recap` method, Recap aliases are transformed into Avro `aliases` entries. The process works as follows:

1. If the alias in the Recap type does not contain a dot (i.e., it's a built-in type), it's resolved to the corresponding Avro type. For instance, "int32" is converted to "int", "decimal256" to a "bytes" type with a logicalType of "decimal", and so forth.
2. If the alias in the Recap type contains a dot (indicating a fully-qualified, non-built-in type), it's used as a direct reference in the Avro schema.

Here's an example:

```python
from recap.types import IntType
from recap.converters.avro import AvroConverter

avro_converter = AvroConverter()
recap_schema = IntType(bits=32, alias="myNamespace.myAlias")
avro_schema = avro_converter.from_recap(recap_schema)

print(avro_schema)  # Output: {'type': 'int', 'aliases': ['myNamespace.myAlias']}
```

## Namespaces

The `to_recap` method provides the ability to handle Avro namespaces when converting Avro schemas to Recap schemas.

The namespace is incorporated into the alias of the Recap schema in the format "namespace.name", ensuring the uniqueness of each schema, especially in the context of complex or nested schemas.

Namespace resolution order is as follows:

1. Use `namespace` field in Avro schema, if available.
2. Use dotted `name` (`foo.bar.Baz`) in Avro schema, if available.
3. Use `namespace` argument in `to_recap` method call, if available.
4. Use default namespace `_root`.

Here's an example:

```python
from recap.converters.avro import AvroConverter

avro_schema_str = '{"type": "record", "name": "exampleNS.myRecord", "fields": []}'
recap_schema = AvroConverter().to_recap(avro_schema_str)
print(recap_schema.alias)  # Output: "exampleNS.myRecord"
```

## Limitations and Constraints

The Recap type system is more expressive than the Avro type system. Consequently, there are some Recap types which cannot be represented in Avro, or which are represented less precisely. For instance, IntType with `bits > 64` cannot be represented in Avro, as Avro only supports 32-bit signed ints and 64-bit signed longs.

The conversion functions raise a `ValueError` exception if the conversion is not possible.
