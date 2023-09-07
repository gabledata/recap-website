---
layout: default
title: "PostgreSQL"
parent: "Integrations"
---

# PostgreSQL
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap add my_pg postgresql://postgres:password@localhost:5432
```

### Environment Variables

```bash
export RECAP_SYSTEM__MY_PG=postgresql://postgres:password@localhost:5432
```

### Python API

```python
from recap.clients import create_client

with create_client("postgresql://postgres:password@localhost:5432") as client:
    client.ls("testdb")
```

## Format

### URLs

Recap's PostgreSQL client takes a PostgreSQL URL with an optional DB in the path.

### Paths

Recap's PostgreSQL paths are formatted as:

```
[system]/[database]/[schema]/[table]
```

{: .note }
The `schema` component is not a data model schema. It's [PostgreSQL's schema](https://www.postgresql.org/docs/current/ddl-schemas.html), which is similar to a namespace. `schema` is usually `public`.

## Type Conversion

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

The conversion functions raise a `ValueError` exception if the conversion is not possible.
