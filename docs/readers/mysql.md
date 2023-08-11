---
layout: default
title: "MySQL"
parent: "Readers"
---

# MySQL
{: .no_toc }

1. TOC
{:toc}

The `MysqlReader` class is used to convert MySQL table schemas to Recap types. The main method in this class is `to_recap`.

## `to_recap`

```python
def to_recap(self, table: str, schema: str, catalog: str) -> StructType
```

The `to_recap` method takes in the name of a MySQL table, schema, and catalog, and returns a Recap `StructType` that represents the PostgreSQL table schema.

{: .note }
MySQL's schema and catalog semantics are non-standard. The `schema` parameter is MySQL's database. The `catalog` parameter should always be [`"def"`](https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html). These naming conventions are included for consistency with other readers.

### Example

```python
from mysql.connector import connect
from recap.readers.mysql import MysqlReader

connection = connect(database="my_database", user="my_user", password="my_password")
recap_schema = MysqlReader(connection).to_recap("my_table", "my_database", "def")
```

In this example, `recap_schema` will be a `StructType` that represents the schema of `my_table` in the `my_database` database.

## Type Conversion

This table shows the corresponding Recap types for each MySQL type, along with the associated attributes:

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

The conversion functions raise a `ValueError` exception if the conversion is not possible due to the PostgreSQL data type being unknown.

JSON column types are converted to Recap `StringType` with `variable=True` and `bytes_=OCTET_LENGTH`. This is not technically accurate, since the JSON type is a binary type that includes the structure of the JSON data. This is the best approximation.

BIT columns are always converted to a BytesType with `variable=False` and `bytes_=8`.