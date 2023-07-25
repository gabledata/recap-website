---
layout: default
title: "Snowflake"
parent: "Readers"
---

# Snowflake
{: .no_toc }

1. TOC
{:toc}

The `SnowflakeReader` class is used to convert Snowflake table schemas to Recap types. The main method in this class is `to_recap`.

## `to_recap`

```python
def to_recap(self, table: str, schema: str, catalog: str) -> StructType
```

The `to_recap` method is used to translate a specific Snowflake table to a `StructType` (a Recap type). The method takes the table name, schema, and catalog as arguments and uses these to query the Snowflake `information_schema.columns` view for the metadata of the specified table. It constructs a `StructType` from these column definitions, converting each column to the corresponding Recap type.

## Type Conversion

This table shows the corresponding Recap types for each Snowflake type, along with the associated attributes:

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

The conversion functions raise a `ValueError` exception if the conversion is not possible due to the Snowflake data type being unknown.