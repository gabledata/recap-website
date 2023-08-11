---
title: Home
layout: default
nav_exclude: true
---

# Recap
{: .fs-9 }

Recap is a Python library that reads and writes schemas from web services, databases, and schema registries in a standard format.
{: .fs-6 .fw-300 }

[View on GitHub](https://github.com/recap-build/recap){: .btn .fs-5 .mb-4 .mb-md-0 }

## Use Cases

* Compare schemas
* Check schema compatibility
* Store schemas in a catalog or registry
* Transpile schemas
* Transform schemas

## Supported Formats

| Format      | Read | Write |
| ----------- | ----------- | ----------- |
| [Avro](https://recap.build/docs/converters/avro/) | ✅ | ✅ |
| [Protobuf](https://recap.build/docs/converters/protobuf/) | ✅ | ✅ |
| [JSON Schema](https://recap.build/docs/converters/json-schema/) | ✅ |  |
| [Snowflake](https://recap.build/docs/readers/snowflake/) | ✅ |  |
| [PostgreSQL](https://recap.build/docs/readers/postgresql/) | ✅ |  |
| [MySQL](https://recap.build/docs/readers/mysql/) | ✅ |  |
| [BigQuery](https://recap.build/docs/readers/bigquery/) | ✅ |  |
| [Confluent Schema Registry](https://recap.build/docs/readers/confluent-schema-registry/) | ✅ |  |
| [Hive Metastore](https://recap.build/docs/readers/hive-metastore/) | ✅ |  |

## Supported Types

Recap borrows types from [Apache Arrow](https://arrow.apache.org/)'s [Schema.fbs](https://github.com/apache/arrow/blob/main/format/Schema.fbs) and [Apache Kafka](https://kafka.apache.org/)'s [Schema.java](https://github.com/apache/kafka/blob/trunk/connect/api/src/main/java/org/apache/kafka/connect/data/Schema.java).

* null
* bool
* int
* float
* string
* bytes
* list
* map
* struct
* enum
* union

## Recap Format

Recap schemas can be stored in YAML, TOML, or JSON formats using [Recap's type spec](/spec). Here’s a YAML example:

```yaml
type: struct
fields:
  - name: id
    type: int
    bits: 64
    signed: false
  - name: email
    type: string
    bytes: 255
```
