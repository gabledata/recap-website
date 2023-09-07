---
title: Python
layout: default
nav_order: 7
---

# Python
{: .no_toc}

Programming with Recap.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Overview

Recap is written in Python and is designed to be used as a Python library.

There are three main components:

* [`recap.clients`](https://github.com/recap-build/recap/tree/main/recap/clients) - Recap clients for connecting to systems and reading schemas.
* [`recap.converters`](https://github.com/recap-build/recap/tree/main/recap/converters) - Recap converters for converting schemas to and from Recap schemas.
* [`recap.types`](https://github.com/recap-build/recap/tree/main/recap/types.py) - Recap's type system.

{: .note }
If you're unfamiliar with clients, converters, and types, read Recap's [concepts](/docs/concepts) page.

## Clients

Clients connect to remote systems, read schemas, and conver them to Recap schemas.

You can connect using `create_client`:

```python
from recap.clients import create_client

with create_client('postgresql://user:pass@host:port/dbname') as client:
    dbs = client.ls()
    struct = client.get_schema("testdb", "public", "users")
```

See the [integrations](/docs/integrations) page for each systme's URL format.

## Converters

Converters are used to convert to and from Recap schemas. Unlike clients, converters do not connect to remote systems. They just take an in-memory schema and return a Recap schema (or vice-versa).

Here's how to convert a Protobuf schema to a Recap schema:

```python
from recap.converters.protobuf import ProtobufConverter

proto_schema = """
syntax = "proto3";

message User {
    string name = 1;
    int32 age = 2;
}
"""
recap_schema = ProtobufConverter().to_recap(proto_schema)
```

{: .note }
Each converter has a `to_recap` and/or `from_recap` method. The method parameters vary depending on the converter. See the [integrations](/docs/integrations) page for specifics on each converter.

## Types

Recap's [Type Spec](/specs/type/) is implemented in [`types.py`](https://github.com/recap-build/recap/tree/main/recap/types.py). This code is Recap's [abstract syntax tree](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (AST). If you want to manipulate schemas, you need to get familar with this file.

The two most important methods are `to_dict` and `from_dict`. These methods convert Recap's [concrete syntax tree](https://en.wikipedia.org/wiki/Parse_tree) (CST) to and from Recap's AST.

{: .note }
We're getting a bit academic here, so let's be specific about AST vs. CST. Recap's AST is defined in its `*Type` classes (`StructType`, `IntType`, and so on). Recap's CST is contained in a dictionary following the syntax defined in the [Type Spec](/specs/type/) (`{"type": "int", "bits": 32}`). Type systems have both because the AST is easier to work with, but the CST is easier to serialize.

### Converting a CST to an AST

Use `from_dict` to convert a concrete syntax tree (in a dictionary) to an abstract syntax tree:

```python
from recap.types import from_dict

cst = {
    "type": "struct",
    "fields": [
        {
            "type": "int",
            "bits": 32,
        },
        {
            "type": "string",
            "bytes": 50,
        },
    ],
}
ast = from_dict(cst)
```

### Converting an AST to a CST

Use `to_dict` to convert an abstract syntax tree to a concrete syntax tree (in a dictionary):

```python
from recap.types import to_dict

ast = StructType(
    fields=[
        IntType(bits=32),
        StringType(bytes_=50),
    ]
)
cst = to_dict(ast)
```

### Working with Schemas

Transforming schemas to add, remove, or modify fields is a common operation. You can manipulate schemas in Recap's AST, CST, or in the system's native schema format. Recap doesn't care.

Let's look at some AST examples.

{: .note }
There are a lot of examples in Recap's [`test_types.py`](https://github.com/recap-build/recap/tree/main/tests/unit/test_types.py) file. Take a look if you want to see more.

#### Create a Struct
{: .no_toc }

This code creates a struct with an `int32` field. The field is required (it's not nullable and has no default).

```python
from recap.types import StructType, IntType

struct = StructType(
    fields=[
        IntType(bits=32),
    ]
)
```

#### Make Fields Optional
{: .no_toc }

This code creates a struct with a single optional field.

```python
from recap.types import StructType, IntType, UnionType, NullType

StructType(
    fields=[
        UnionType(
            default=None,
            types=[
                NullType(),
                IntType(bits=32),
            ],
        )
    ]
)
```

{: .note }
Optional fields are represented as a union of `null` and the field type with a default set to `None`.

#### Name Fields
{: .no_toc }

Struct fields usually have a name, so let's add one.

```python
from recap.types import StructType, IntType, UnionType, NullType

StructType(
    fields=[
        UnionType(
            default=None,
            types=[
                NullType(),
                IntType(bits=32),
            ],
            name="age",
        )
    ]
)
```

#### Adding a Field
{: .no_toc }

Let's add a `email` field to our struct.

```python
from recap.types import StructType, IntType, UnionType, NullType, StringType

ast = StructType(
    fields=[
        UnionType(
            default=None,
            types=[
                NullType(),
                IntType(bits=32),
            ],
            name="age",
        ),
    ]
)

ast.fields.append(StringType(bytes_=50, name="email"))
```

{: .note }
The email field is required. It's not nullable and has no default.

#### Removing a Field
{: .no_toc }

Let's add a `email` field to our struct.

```python
from recap.types import StructType, IntType, UnionType, NullType, StringType

ast = StructType(
    fields=[
        UnionType(
            default=None,
            types=[
                NullType(),
                IntType(bits=32),
            ],
            name="age",
        ),
        StringType(bytes_=50, name="email"),
    ]
)

ast.fields = [field for field in ast.fields if field.extra_attrs["name"] != "email"]
```

{: .note }
This example modifies the AST in-place. The `*Types` are mutable, but it's safer to treat the AST objects as immutable by creating new objects.

#### Modifying a Field
{: .no_toc }

Let's modify the `email` length to 100 bytes.

```python
from recap.types import StructType, IntType, UnionType, NullType, StringType

ast = StructType(
    fields=[
        UnionType(
            default=None,
            types=[
                NullType(),
                IntType(bits=32),
            ],
            name="age",
        ),
        StringType(bytes_=50, name="email"),
    ]
)

for field in ast.fields:
    if field.extra_attrs["name"] == "email":
        field.bytes_ = 100
```
