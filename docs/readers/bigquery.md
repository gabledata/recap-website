---
layout: default
title: "BigQuery"
parent: "Readers"
---

# BigQuery
{: .no_toc }

1. TOC
{:toc}

The `BigQueryReader` class is used to convert BigQuery table schemas to Recap types. The main method in this class is `to_recap`.

## `to_recap`

```python
def to_recap(self, dataset: str, table: str) -> StructType
```

The `to_recap` method takes in the name of a BigQuery dataset and table, and returns a Recap `StructType` that represents the BigQuery table schema.

### Example

```python
from google.cloud import bigquery
from recap.readers.bigquery import BigQueryReader

client = bigquery.Client()
recap_schema = BigQueryReader(client).to_recap("my_dataset", "my_table")
```

In this example, `recap_schema` will be a `StructType` that represents the schema of `my_table` in `my_dataset`.

## Type Conversion

This table shows the corresponding Recap types for each BigQuery type, along with the associated attributes:

| BigQuery Type | Recap Type |
|---------------|------------------------------------|
| STRING, JSON | StringType (bytes <= 65_536) |
| BYTES | BytesType (bytes <= 65_536) |
| INT64, INTEGER, INT, SMALLINT, TINYINT, BYTEINT | IntType (bits=64, signed=True) |
| FLOAT, FLOAT64 | FloatType (bits=64) |
| BOOLEAN | BoolType |
| TIMESTAMP, DATETIME | IntType (logical="build.recap.Timestamp", bits=64, unit="microsecond") |
| TIME | IntType (logical="build.recap.Time", bits=32, unit="microsecond") |
| DATE | IntType (logical="build.recap.Date", bits=32, unit="day") |
| RECORD, STRUCT | StructType |
| NUMERIC, DECIMAL | BytesType (logical="build.recap.Decimal", bytes=16, variable=False, precision <= 38, scale <= 0) |
| BIGNUMERIC, BIGDECIMAL | BytesType (logical="build.recap.Decimal", bytes=32, variable=False, precision <= 76, scale <= 0) |

## Limitations and Constraints

The conversion functions raise a `ValueError` exception if the conversion is not possible.