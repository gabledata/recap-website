---
layout: default
title: "BigQuery"
parent: "Integrations"
---

# BigQuery
{: .no_toc }

1. TOC
{:toc}

## Connecting

### CLI

```bash
recap ls bigquery://
recap schema bigquery://some-project/some-dataset/some-table
```

### Python API

```python
from recap.clients import create_client

with create_client("bigquery://") as client:
    client.ls("my_project")
    client.schema("some-project", "some-dataset", "some-table")
```

## URLs

Recap's BigQuery URL format:

```
bigquery://[project]/[dataset]/[table]
```

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