---
layout: default
title: "Hive Metastore"
parent: "Integrations"
---

# Hive Metastore
{: .no_toc }

1. TOC
{:toc}

The `HiveMetastoreReader` class is used to convert Hive table schemas into Recap types. This class can also be used to fetch and convert table statistics from Hive Metastore.

## `to_recap`

```python
def to_recap(
    self,
    database_name: str,
    table_name: str,
    include_stats: bool = False,
) -> StructType
```

The `to_recap` method takes in the name of a database and a table within that database, retrieves the associated schema from the Hive Metastore, and converts it into a Recap `StructType`. If `include_stats` is set to True, the method will also fetch table statistics from the Hive Metastore and include them in the returned `StructType`.

### Example

```python
from pymetastore.metastore import HMS
from recap.readers.hive_metastore import HiveMetastoreReader

with HMS.create("localhos", 9093) as client:
  recap_schema = HiveMetastoreReader(client).to_recap("my_database", "my_table")
```

In this example, `recap_schema` will be a `StructType` that represents the schema of the `my_table` table in the `my_database` database.

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