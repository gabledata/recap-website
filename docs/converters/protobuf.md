---
layout: default
title: "Protobuf"
parent: "Converters"
---

# Protobuf
{: .no_toc }

1. TOC
{:toc}

Recap uses the `ProtobufConverter` class to convert between Recap types and Protobuf types. `ProtobufConverter` has two main methods: `from_recap` and `to_recap`.

## `from_recap`

This method converts a Recap type to a Protobuf type.

```python
def from_recap(self, struct_type: StructType) -> File:
```

The `from_recap` method checks whether the input Recap type has an alias and collects all the necessary Message and Enum types that need to be declared in the Proto file. It verifies all subpackages are under the root package and constructs the proto file with the correct nesting.

## `to_recap`

This method converts a Protobuf type to a Recap type.

```python
def to_recap(
    self,
    protobuf_schema_str: str,
) -> StructType:
```

The `to_recap` method is designed to take in Protobuf types and translate them into equivalent Recap types.

## Examples

### Recap to Protobuf

```python
from recap.converters.protobuf import ProtobufConverter
from recap.types import StructType, StringType, IntType

# Define a Recap schema
recap_schema = StructType(
    alias="recap.build.Person",
    fields=[
        StringType(name="name"),
        IntType(name="age", bits=32),
    ],
)

# Convert to Protobuf schema
protobuf_schema = ProtobufConverter().from_recap(recap_schema)
```

### Protobuf to Recap

```python
from recap.converters.protobuf import ProtobufConverter

# Define an Protobuf schema
protobuf_schema = """
syntax = "proto3";
package build.recap.example;
message Person {
    string name = 1;
    int32 age = 2;
}
"""

# Convert to Recap schema
recap_schema = ProtobufConverter().to_recap(protobuf_schema)
```

## Type Conversion

### From Recap to Protobuf

This table shows the corresponding Protobuf types for each Recap type.

| Recap Type (with attribute limits) | Protobuf Type |
|------------------------------------|---------------|
| NullType                           | google.protobuf.NullValue |
| BoolType                           | bool |
| IntType (signed and bits <= 32)    | int32 |
| IntType (signed and bits <= 64)    | int64 |
| IntType (unsigned and bits <= 32)  | uint32 |
| IntType (unsigned and bits <= 64)  | uint64 |
| FloatType (bits <= 32)             | float |
| FloatType (bits <= 64)             | double |
| StringType (bytes <= 2_147_483_647)| string |
| BytesType (bytes <= 2_147_483_647) | bytes |
| ListType                           | Repeated Field |
| MapType                            | MapField |
| UnionType                          | OneOf |
| StructType                         | Message |
| EnumType                           | Enum |

### From Protobuf to Recap

This table shows the corresponding Recap types for each Protobuf type.

| Protobuf Type | Recap Type |
|---------------|------------|
| google.protobuf.NullValue | NullType |
| bool | BoolType |
| int32 | IntType(signed=True, bits=32) |
| int64 | IntType(signed=True, bits=64) |
| uint32 | IntType(signed=False, bits=32) |
| uint64 | IntType(signed=False, bits=64) |
| float | FloatType(bits=32) |
| double | FloatType(bits=64) |
| string | StringType(bytes=2_147_483_647, variable=True) |
| bytes | BytesType(bytes=2_147_483_647, variable=True) |
| Repeated Field | ListType |
| MapField | MapType |
| OneOf | UnionType |
| Message | StructType |
| Enum | EnumType |

## Aliases

Recap aliases are converted to Protobuf `package` and `message` names. An alias `foo.bar.Baz` is converted to a Protobuf message called `Baz` in the `foo.bar` package. Likewise, a Protobuf message called `Baz` in the `foo.bar` package is converted to a Recap alias `foo.bar.Baz`.

## Limitations and Constraints

The Recap type system is more expressive than the Protobuf type system. Consequently, there are some Recap types which cannot be represented in Protobuf, or which are represented less precisely. For instance, IntType with bits > 64 cannot be represented in Protobuf, as Protobuf only supports 32-bit and 64-bit integers.

The conversion functions raise a ValueError exception if the conversion is not possible.
