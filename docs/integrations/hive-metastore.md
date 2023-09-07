---
layout: default
title: "Hive Metastore"
parent: "Integrations"
---

# Hive Metastore
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap add my_hms thrift+hms://hive:password@localhost:9083
```

### Environment Variables

```bash
export RECAP_SYSTEM__MY_HMS=thrift+hms://hive:password@localhost:9083
```

### Python API

```python
from recap.clients import create_client

with create_client("thrift+hms://hive:password@localhost:9083") as client:
    client.ls("testdb")
```

## Format

### URLs

Recap's Hive Metastore client takes the Thrift URL to the Hive Metastore.

{: .note }
The scheme must be `thrift+hms`. The `+hms` suffix is required to distinguish this client from other clients that also use Thrift connections (similar to [SQLAlchemy's `dialect+driver` format](https://docs.sqlalchemy.org/en/20/core/engines.html#database-urls))

### Paths

Recap's Confluent Schema Registry paths are formatted as:

```
[system]/[database]/[table]
```

## Type Conversion

| Hive Type                          | Recap Type |
|------------------------------------|------------------------------------|
| BOOLEAN        | BoolType |
| BYTE           | IntType (bits=8) |
| SHORT          | IntType (bits=16) |
| INT            | IntType (bits=32) |
| LONG           | IntType (bits=64) |
| FLOAT          | FloatType (bits=32) |
| DOUBLE         | FloatType (bits=64) |
| VOID           | NullType |
| STRING         | StringType (bytes <= 9_223_372_036_854_775_807) |
| BINARY         | BytesType (bytes <= 2_147_483_647) |
| DECIMAL                     | BytesType (logical="build.recap.Decimal", bytes=16, variable=False, precision, scale) |
| VARCHAR                     | StringType (bytes=length) |
| CHAR                        | StringType (bytes=length, variable=False) |
| DATE           | IntType (logical="build.recap.Date", bits=32, signed=True, unit="day") |
| TIMESTAMP      | IntType (logical="build.recap.Timestamp", bits=64, signed=True, unit="nanosecond", timezone="UTC") |
| TIMESTAMPLOCALTZ| IntType (logical="build.recap.Timestamp", bits=64, signed=True, unit="nanosecond", timezone=None) |
| INTERVAL_YEAR_MONTH | BytesType (logical="build.recap.Interval", bytes=12, signed=True, unit="month") |
| INTERVAL_DAY_TIME | BytesType (logical="build.recap.Interval", bytes=12, signed=True, unit="second") |
| MAP                         | MapType |
| ARRAY                        | ListType |
| UNIONTYPE                       | UnionType |
| STRUCT                      | StructType |

## Limitations and Constraints

The conversion functions raise a `ValueError` exception if the conversion is not possible.
