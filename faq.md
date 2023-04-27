---
layout: default
title: "A Schema Language Compatible With JSON, Protobuf, Avro, SQL, Parquet, Arrow, and More..."
custom_css: spec
---

# Recap Frequently Asked Questions

### Is this a serialization format?

No. Recap does not define how data is persisted to disk. It's meant to be more of a translation layer to work with data records without worrying whether they came from Avro, JSON, Protobuf, Parquet, or a database.

### May I define extra type attributes?

Yes. Recap will ignore them.

### Why did you include enums?

Recap's type system is constrained enough that enums can't be expressed as type unions anymore. In a more expressive type system you could say an enum was a union of constant strings (literals). That's out the window since Recap doesn't have a literal.

### Why didn't you include constraints?

I plan to add constraints to Recap as a separate add-on spec (similar to [JSON schema's validators](https://json-schema.org/draft/2020-12/json-schema-validation.html), [protoc-gen-validate](https://github.com/bufbuild/protoc-gen-validate), and [JSR 380](https://beanvalidation.org/2.0-jsr380/)). The constraints will be *very* modest.

I made this decision because I found mixing constraints and types to be difficult. Defining an `int` that's >= 0 and <= 4_294_967_296 is the same range as a uint32, but doesn't necessarily say anything about the physical format. When you start adding in floats, IEEE standards, and arbitrary precision decimals, these distinctions are important. While they could be encoded in a pure type system, I felt making them explicit--including `bits` as an attribute, for example--was more pragmatic. It also aligned with Arrow's `Schema.fbs` does.

Once I committed to keeping constraints separate from types, it felt more natural to keep constraints out of the type spec all together.

### Why didn't you limit integer and float bits to specific values (16, 32, 64, and so on)?

I couldn't find a good reason to. The common bit lengths are implemented as aliases.

### Why is it called a `struct`?

Struct, record, schema, and object are all common. A "schema" containing a "schema" is strange and "objects" usually have methods. "record" could have worked, but seemed less well known. Thus, I went with "struct".

### Why didn't you use a more expressive type system like CUE?

Frankly, I'm not an academic and I don't have enough specialty in type systems to contribute to that area. I did spend time with CUE (and a little with KCL), and found them to be unwieldy. I didn't want templating, and I only want to include a small set of constraints in Recap. Elaborate type systems and templating felt like overkill.

Kafka Connect is a schema example that is much more constrained than something like CUE, while still modeling most of what Recap wants to model. Kafka Connect has [hundreds of source and sink connectors](https://docs.confluent.io/cloud/current/connectors/index.html#preview-connectors).

[Apache Arrow](https://arrow.apache.org) is in the same vein as Kafka Connect. In fact, Recap's base types and many of its `alias`es are taken directly from Apache Arrow. Again, Arrow has proven to work with dozens of different databases and data frameworks.

### Why didn't you use Apache Arrow?

For starters, Arrow doesn't have a schema definition language; it's entirely programmatic. Their spec is their [Schema.fbs](https://github.com/apache/arrow/blob/main/format/Schema.fbs). You'd have to write code to define a schema; this isn't what I wanted.

In addition, their in-memory model is more optimized for columnar analytical work. Recap's main focus is on record-based data from APIs, message buses, and databases. Single record operations and schema manipulation tend to be different than analytical operations. Counting and grouping doesn't make much sense on a single record. Schema manipulation is where Recap shines: projecting out fields, merging and intersecting schemas, and checking for schema compatibility.

### How do you handle coercion?

Coercing types that don't fit cleanly into Recap's type system is outside the scope of this spec. Recap will include a test suite that defines how specific types are meant to be converted to and from Recap types on a per-SDL basis. For example, converting PostgreSQL's `geography(point)` type to/from a Recap type is something that will be defined in the test suite and documented in the PostgreSQL converter documentation.
