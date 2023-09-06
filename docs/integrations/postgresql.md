---
layout: default
title: "PostgreSQL"
parent: "Integrations"
---

# PostgreSQL
{: .no_toc }

1. TOC
{:toc}

The `PostgresqlReader` class is used to convert PostgreSQL table schemas to Recap types. The main method in this class is `to_recap`.

## `to_recap`

```python
def to_recap(self, table: str, schema: str, catalog: str) -> StructType
```

The `to_recap` method takes in the name of a PostgreSQL table, schema, and catalog, and returns a Recap `StructType` that represents the PostgreSQL table schema.

### Example

```python
from psycopg2 import connect
from recap.readers.postgresql import PostgresqlReader

connection = connect(database="my_database", user="my_user", password="my_password")
recap_schema = PostgresqlReader(connection).to_recap("my_table", "my_schema", "my_catalog")
```

In this example, `recap_schema` will be a `StructType` that represents the schema of `my_table` in `my_schema` within `my_catalog`.

## Type Conversion

This table shows the corresponding Recap types for each PostgreSQL type, along with the associated attributes:

| PostgreSQL Type | Recap Type |
|-----------------|------------------------------------|
| bigint, int8, bigserial, serial8 | IntType (bits=64, signed=True) |
| integer, int, int4, serial, serial4 | IntType (bits=32, signed=True) |
| smallint, smallserial, serial2 | IntType (bits=16, signed=True) |
| double precision, float8 | FloatType (bits=64) |
| real, float4 | FloatType (bits=32) |
| boolean | BoolType |
| text, json, jsonb, character varying, varchar | StringType (bytes_=OCTET_LENGTH, variable=True) |
| char | StringType (bytes_=OCTET_LENGTH, variable=False) |
| bytea, bit varying | BytesType (bytes_=MAX_FIELD_SIZE, variable=True) |
| bit | BytesType (bytes_=ceil(BIT_LENGTH / 8), variable=False) |
| timestamp | IntType(bits=64, logical="build.recap.Timestamp", unit=unit) |
| decimal, numeric | BytesType(logical="build.recap.Decimal", bytes_=32, variable=False, precision=NUMERIC_PRECISION, scale=NUMERIC_SCALE) |

## Limitations and Constraints

The conversion functions raise a `ValueError` exception if the conversion is not possible due to the PostgreSQL data type being unknown.
