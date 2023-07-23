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
| [Avro](https://avro.apache.org) | ✅ | ✅ |
| [Protobuf](https://protobuf.dev) | ✅ | ✅ |
| [JSON Schema](https://json-schema.org) | ✅ |  |
| [Snowflake](https://www.snowflake.com) | ✅ |  |
| [PostgreSQL](https://www.postgresql.org) | ✅ |  |
| [BigQuery](https://cloud.google.com/bigquery) | ✅ |  |
| [Confluent Schema Registry](https://github.com/confluentinc/schema-registry) | ✅ |  |
| [Hive Metastore](https://cwiki.apache.org/confluence/display/hive/design#Design-Metastore) | ✅ |  |

## Supported Types

Recap borrows types from [Apache Arrow](https://arrow.apache.org/)'s [Schema.fbs](https://github.com/apache/arrow/blob/main/format/Schema.fbs) and [Apache Kafka](https://kafka.apache.org/)'s [Schema.java](https://github.com/apache/kafka/blob/trunk/connect/api/src/main/java/org/apache/kafka/connect/data/Schema.java).

* null
* list
* bool
* map
* int
* struct
* float
* enum
* string
* union
* bytes

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
