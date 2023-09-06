---
layout: default
title: "Confluent Schema Registry"
parent: "Integrations"
---

# Confluent Schema Registry
{: .no_toc }

1. TOC
{:toc}

The `ConfluentRegistryReader` class is used to convert schemas registered in a Confluent Schema Registry to Recap types. The main method in this class is `to_recap`.

## `to_recap`

```python
def to_recap(self, topic: str) -> StructType
```

The `to_recap` method takes in the name of a Kafka topic, fetches the associated schema from the Confluent Schema Registry, and converts it to a Recap `StructType`. The method supports Avro, JSON, and Protobuf schemas.

### Example

```python
from confluent_kafka.schema_registry import SchemaRegistryClient
from recap.readers.confluent_registry import ConfluentRegistryReader

registry = SchemaRegistryClient({"url": "http://my-registry:8081"})
recap_schema = ConfluentRegistryReader(registry).to_recap("my_topic")
```

In this example, `recap_schema` will be a `StructType` that represents the schema of the value of messages in `my_topic`.

## Type Conversion

The `to_recap` method uses the `AvroConverter`, `JSONSchemaConverter`, and `ProtobufConverter` classes to convert schemas, based on their type. 

Please see the individual documentation for these classes for information on how they convert types:

- Avro: [AvroConverter]({{site.baseurl}}/docs/converters/avro)
- JSON schema: [JSONSchemaConverter]({{site.baseurl}}/docs/converters/json-schema)
- Protocol Buffers: [ProtobufConverter]({{site.baseurl}}/docs/converters/protobuf)

## Limitations and Constraints

1. ConfluentRegistryReader does not support [schema references](https://docs.confluent.io/platform/current/schema-registry/fundamentals/serdes-develop/index.html#schema-references).

The conversion functions raise a `ValueError` exception if the conversion is not possible.