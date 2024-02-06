---
layout: default
title: "SQLite"
parent: "Integrations"
---

# SQLite
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap ls sqlite:///path/to/database.db
recap schema sqlite:///path/to/database.db/table
```

### Python API

```python
from recap.clients import create_client

with create_client("sqlite:///path/to/database.db") as client:
    client.ls()
    client.schema("table")
```

## URLs

Recap's SQLite client takes a SQLite URL with the path to the database file.

```
sqlalchemy://[path*]/[table]
```

{: .note }
Similar to SQLAlchemy, Recap's SQLite URLs can take a `uri=true` query parameter to enable [SQLite's URI format](https://sqlite.org/uri.html). Other [sqlite3.connect](https://docs.python.org/3/library/sqlite3.html#sqlite3.connect) parameters may be passed as query parameters as well.

## Type Conversion

SQLite has a dynamic type system. The Recap SQLite client maps SQLite's column affinities to Recap types as follows:

| SQLite Affinity | Recap Type |
|-----------------|------------------------------------|
| INTEGER | IntType (bits=64) |
| REAL | FloatType (bits=32 or 64, depending on precision) |
| TEXT | StringType (bytes_=CHARACTER_OCTET_LENGTH or MAX) |
| BLOB | BytesType (bytes_=CHARACTER_OCTET_LENGTH or MAX) |
| NUMERIC | UnionType (includes NullType, IntType, FloatType, StringType, BytesType) |

See SQLAlchemy's [Determiniation of Column Affinity](https://www.sqlite.org/datatype3.html#determination_of_column_affinity) section for a complete list of string-matching rules from column type to column affinity.

`CHARACTER_OCTET_LENGTH` is determined based on the defined column type's length (e.g. `VARCHAR(255)`). If unset, SQLite's maximum length (2^31-1 bytes) is used.

As noted in the `REAL` affinity, the precision of the `FloatType` is determined by the precision of the column's type. If a REAL's precision is `<= 23`, a 32-bit float is used, otherwise a 64-bit float is used.

## Limitations and Constraints

The conversion functions raise a ValueError exception if the conversion is not possible due to unsupported column types or if specific SQLite type characteristics cannot be accurately represented in Recap types.

SQLite does not have a native BOOLEAN type, boolean values are stored as INTEGER and should be handled accordingly in application logic.

SQLite also does not have DATE, DATETIME, TIMESTAMP, or TIME. These types have NUMERIC column affinities.