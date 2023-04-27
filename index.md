---
layout: default
title: "A Schema Language Compatible With JSON, Protobuf, Avro, SQL, Parquet, Arrow, and More..."
---

<div class="md:w-3/4 text-center m-auto">
  <h2 class="text-2xl leading-normal">
    A Schema Language Compatible With JSON, Protobuf, Avro, SQL, Parquet, Arrow, and More...
  </h2>

  <p class="w-5/6 md:w-3/4 mx-auto my-4">
    Recap describes database tables and serialization formats with minimal type coercion.
  </p>

  <a href="/spec" class="bg-black text-white px-16 py-4 inline-block mt-2">
    Read the Spec
  </a>

  <div class="w-5/6 md:w-2/3 mx-auto my-36">
    <h3 class="text-xl my-2">Types</h3>
    <p>
      Recap borrows types from <a href="https://arrow.apache.org/">Apache Arrow</a>'s <a href="https://github.com/apache/arrow/blob/main/format/Schema.fbs">Schema.fbs</a> and <a href="https://kafka.apache.org/">Apache Kafka</a>'s <a href="https://github.com/apache/kafka/blob/trunk/connect/api/src/main/java/org/apache/kafka/connect/data/Schema.java">Schema.java</a>.
    </p>
    <div class="grid grid-cols-2 gap-0 my-4 w-5/6 md:w-1/2 mx-auto text-left">
      <div>▪ null</div><div>▪ list</div>
      <div>▪ bool</div><div>▪ map</div>
      <div>▪ int</div><div>▪ struct</div>
      <div>▪ float</div><div>▪ enum</div>
      <div>▪ string</div><div>▪ union</div>
      <div>▪ bytes</div>
    </div>    
  </div>

  <div class="w-5/6 md:w-2/3 mx-auto my-36">
    <h3 class="text-xl my-2">Format</h3>
    <p>
      Recap schemas can be defined in YAML, TOML, JSON, XML, or any other compatible language. Here’s a YAML example.
    </p>
    <pre class="whitespace-pre text-left mx-auto inline-block text-sm">
      <code class="-ml-8">
type: struct
fields:
  - name: id
    type: int
    bits: 64
    signed: false
  - name: email
    type: string
    bytes: 255
      </code>
    </pre>
  </div>

  <div class="w-5/6 md:w-2/3 mx-auto my-36">
    <h3 class="text-xl my-2">Use Cases</h3>
    <p>
      Build one set of schema management tools for your whole ecosystem.
    </p>
    <ul class="list-none inline-block m-auto text-left my-6">
      <li>▪ Compare schemas</li>
      <li>▪ Check schema compatibility</li>
      <li>▪ Store schemas in a catalog or registry</li>
      <li>▪ Transpile schemas</li>
      <li>▪ Transform schemas</li>
    </ul>
  </div>

  <div class="w-5/6 md:w-2/3 mx-auto my-36">
    <h3 class="text-xl my-2">Non-Goals</h3>
    <p>
      Recap is a user-friendly, approachable schema language. Recap is not a...
    </p>
    <ul class="list-none inline-block m-auto text-left my-6">
      <li>▪ Serialization format (<a href="https://protobuf.dev">Protobuf</a>, <a href="https://avro.apache.org/">Avro</a>)</li>
      <li>▪ Programmable type system (<a href="https://cuelang.org/">CUE</a>)</li>
      <li>▪ Templating system (<a href="https://jsonnet.org/">Jsonnet</a>)</li>
      <li>▪ In-memory analytics format (<a href="https://arrow.apache.org/">Arrow</a>)</li>
      <li>▪ DB migration tool (<a href="https://alembic.sqlalchemy.org/">Alembic</a>, <a href="https://flywaydb.org/">Flyway</a>)</li>
    </ul>
  </div>
</div>
