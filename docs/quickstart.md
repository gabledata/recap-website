---
title: Quickstart
layout: default
nav_order: 1
---

# Quickstart
{: .no_toc}

Let's get started with Recap.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Install Recap

Start by installing Recap:

```bash
pip install 'recap-core[all]'
```

{: .note }
The `[all]` part will install all of Recap's dependencies, including the optional ones for systems like PostgreSQL, Snowflake, BigQuery, and so on. You might not need all of these dependencies, but they'll be available anyway.

## Read a Schema

Recap has two main commands: `ls` and `schema`. The `ls` command lets you list children of a URL. The URL structure depends on the system type. PostgreSQL URLs look like this:

```
postgresql://user:pass@host:port/[database]/[schema]/[table]
```

{: .note}
The "schema" in the URL is PostgreSQL's [database schema](https://www.postgresql.org/docs/current/ddl-schemas.html), not a table schema. It's usually `public`.

Let's list the schemas for a database called `testdb`:

```bash
recap ls postgresql://user:pass@host:port/testdb
```

```
[
  "pg_toast",
  "pg_catalog",
  "public",
  "information_schema"
]
```

There are four schemas. The `pg_toast` and `pg_catalog` schemas are internal to PostgreSQL. The `information_schema` schema is a standard schema that contains information about the database. The `public` schema is where our tables are located.

```bash
recap ls postgresql://user:pass@host:port/testdb/public
```

```
[
  "test_types"
]
```

This database only has one table, `test_types`. Let's read the schema:

```bash
recap schema postgresql://user:pass@host:port/testdb/public/test_types
```

```json
{
  "type": "struct",
  "fields": [
    {
      "type": "int64",
      "name": "test_bigint",
      "optional": true
    }
  ]
}
```

This is `test_type`'s schema represented as a Recap schema in JSON. The `schema` command reads a schema at the supplied path, converts it to a Recap schema, and prints the Recap schema as a JSON object.

You can also output the schema in [Avro](https://avro.apache.org), [Protobuf](https://protobuf.dev), or [JSON Schema](https://json-schema.org) format using the `--output-format` switch. Here's the same schema in JSONN schema:

```bash
recap schema postgresql://user:pass@host:port/testdb/public/test_types --output-format=json
```

```json
{
  "type": "object",
  "properties": {
    "test_bigint": {
      "default": null,
      "type": "integer"
    }
  }
}
```

## Start Recap's Server

We've been using Recap's CLI to read schemas, but Recap comes with an HTTP/JSON server as well. The server has two parts:

- A [gateway](/docs/gateway/) to list and read schemas. You will find this handy if you're not using Python, or if you want to integrate Recap with other systems.
- A [registry](/docs/registry/) to store and retrieve schemas. This is useful for caching schemas or acting as a repository when using Recap schemas as a source of truth.

Start the server at [http://localhost:8000](http://localhost:8000):

```bash
recap serve
```

### Read Schemas with the Gateway

The server exposes `/gateway/ls` and `/gateway/schema` endpoints that are very similar to the CLI:

```bash
$ curl http://localhost:8000/gateway/ls/postgresql://user:pass@host:port/testdb
```

```json
["pg_toast","pg_catalog","public","information_schema"]
```

And much like the CLI, I can read my `test_types` schema:

```bash
curl http://localhost:8000/gateway/schema/postgresql://user:pass@host:port/testdb/public/test_types
```

```json
{"type":"struct","fields":[{"type":"int64","name":"test_bigint","optional":true}]}
```

{: .warning}
This example includes `user:pass` in the URL. This works, but is not recommended for security reasons. You should configure Recap's server to use the `RECAP_URLS` environment variable instead. See the [configuration](/docs/configuration/#recap_urls) documentation for more details.

{: .note}
Recap's HTTP/JSON gateway does not require a database or any persistence. It just connects to external systems in realtime to fetch schemas.

### Store Schemas in the Registry

The server exposes a series of `/registry` endpoints with [GET/PUT/POST](/docs/registry/#api) methods.

To store a schema in the registry, use the `POST` method:

```bash
curl -X POST \
    -H "Content-Type: application/x-recap+json" \
    -d '{"type":"struct","fields":[{"type":"int64","name":"test_bigint","optional":true}]}' \
    http://localhost:8000/registry/some_schema
```

And to read the schema, use the `GET` method on `/registry/[schema_name]`:

```bash
curl http://localhost:8000/registry/some_schema
```

```json
[{"type":"struct","fields":[{"type":"int64","name":"test_bigint","optional":true}]},4]
```

{: .note}
The schema response includes a version number. This is the schema's version in the registry. The registry will increment this number every time you update the schema.

## Next Steps

You've learned how to install Recap, list and read schemas, and use Recap server's gateway and registry endpoints.

Next, you should look at Recap's [integrations](/docs/integrations) page to learn how to use Recap with other systems. If you're planning on running Recap's server, check out the [gateway](/docs/gateway/) and [registry](/docs/registry/) documentation. Finally, see Recap's [Python](/docs/python) documentation to learn how to use Recap's Python API.