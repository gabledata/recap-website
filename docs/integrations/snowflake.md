---
layout: default
title: "Snowflake"
parent: "Integrations"
---

# Snowflake
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap add my_snowflake snowflake://user:pass@my-account
```

### Environment Variables

```bash
export RECAP_SYSTEM__MY_SNOWFLAKE=snowflake://user:pass@my-account
```

### Python API

```python
from recap.clients import create_client

with create_client("snowflake://user:pass@my-account") as client:
    client.ls("testdb")
```

## Format

### URLs

Recap's Snowflake client uses [Snowflake's SQLAlchemy URL format](https://github.com/snowflakedb/snowflake-sqlalchemy#connection-parameters):

```
snowflake://[user_login_name]:[password]@[account_name]/[database_name]/[schema_name]?warehouse=[warehouse_name]&role=[role_name]
```

### Paths

Recap's PostgreSQL paths are formatted as:

```
[system]/[database]/[schema]/[table]
```

{: .note }
The `schema` component is not a data model schema. It's [Snowflake's schema](https://docs.snowflake.com/en/sql-reference/ddl-database), which is similar to a namespace. `schema` is usually `public`.

## Type Conversion

| Snowflake Type | Recap Type |
|-----------------|------------------------------------|
| float, float4, float8, double, double precision, real | FloatType (bits=64) |
| boolean | BoolType |
| number, decimal, numeric, int, integer, bigint, smallint, tinyint, byteint | BytesType (logical="build.recap.Decimal", bytes_=16, variable=False, precision=NUMERIC_PRECISION, scale=NUMERIC_SCALE) |
| varchar, string, text, nvarchar, nvarchar2, char varying, nchar varying | StringType (bytes_=OCTET_LENGTH, variable=True) |
| char, nchar, character | StringType (bytes_=OCTET_LENGTH, variable=True) |
| binary, varbinary, blob | BytesType (bytes_=OCTET_LENGTH) |
| date | IntType(bits=32, logical="build.recap.Date", unit="day") |
| timestamp, datetime | IntType(bits=64, logical="build.recap.Timestamp", unit=unit) |
| time | IntType(bits=32, logical="build.recap.Time", unit=unit) |

## Limitations and Constraints

The conversion functions raise a `ValueError` exception if the conversion is not possible.
