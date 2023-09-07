---
layout: default
title: "MySQL"
parent: "Integrations"
---

# MySQL
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap add my_sql mysql://mysql:password@localhost:3306
```

### Environment Variables

```bash
export RECAP_SYSTEM__MY_SQL=mysql://mysql:password@localhost:3306
```

### Python API

```python
from recap.clients import create_client

with create_client("mysql://mysql:password@localhost:3306") as client:
    client.ls("testdb")
```

## Format

### URLs

Recap's MySQL client takes a MySQL URL with an optional DB in the path.

### Paths

Recap's MySQL paths are formatted as:

```
[system]/[database]/[table]
```

## Type Conversion

| MySQL Type | Recap Type |
|------------|------------|
| bigint | IntType (bits=64, signed=True) |
| int, integer | IntType (bits=32, signed=True) |
| mediumint | IntType (bits=24, signed=True) |
| smallint | IntType (bits=16, signed=True) |
| tinyint | IntType (bits=8, signed=True) |
| double, (float with precision > 23) | FloatType (bits=64) |
| float (with precision <= 23) | FloatType (bits=32) |
| text, json, mediumtext, longtext, tinytext, varchar | StringType (bytes_=CHARACTER_OCTET_LENGTH, variable=True) |
| char, enum, set | StringType (bytes_=CHARACTER_OCTET_LENGTH, variable=False) |
| blob, mediumblob, longblob, tinyblob, varbinary | BytesType (bytes_=CHARACTER_OCTET_LENGTH, variable=True) |
| binary | BytesType (bytes_=CHARACTER_OCTET_LENGTH, variable=False) |
| bit | BytesType (bytes_=8, variable=False) |
| timestamp, datetime | IntType(bits=64, logical="build.recap.Timestamp", unit=DATETIME_PRECISION) |
| dec, decimal, numeric | BytesType(logical="build.recap.Decimal", bytes_=32, variable=False, precision=NUMERIC_PRECISION, scale=NUMERIC_SCALE) |
| year | IntType (bits=16, signed=False) |

## Limitations and Constraints

1. JSON column types are converted to Recap `StringType` with `variable=True` and `bytes_=OCTET_LENGTH`. This is not technically accurate, since the JSON type is a binary type that includes the structure of the JSON data. This is the best approximation.
2. BIT columns are always converted to a BytesType with `variable=False` and `bytes_=8`.

The conversion functions raise a `ValueError` exception if the conversion is not possible.
