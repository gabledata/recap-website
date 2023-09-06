---
title: Quickstart
layout: default
nav_order: 1
---

# Quickstart
{: .no_toc}

Let's get started with Recap.
{: .fs-6 .fw-300}

In this tutorial, you'll learn how to...

1. [Install](#install) Recap
2. [Connect](#connect) a PostgreSQL database to Recap
3. [Read](#read) a schema from the PostgreSQL database
4. [Start](#serve) Recap's HTTP/JSON gateway

## Install

Start by installing Recap:

```bash
pip install 'recap-core[all]'
```

{: .note }
The `[all]` part will install all of Recap's dependencies, including the optional ones for systems like PostgreSQL, Snowflake, BigQuery, and so on. You might not need all of these dependencies, but they'll be available anyway.

## Connect

You can use Recap's command line interface (CLI) to add systems for Recap to connect to. If you've got a PostgreSQL database running locally, you can add it to Recap like this:

```bash
recap add my-pg postgresql://user:pass@host:port/dbname
```

This will add a system called `my-pg` to Recap.

{: .highlight }
Recap "systems" are just named connections to external systems. They're not the same as the systems themselves. Recap doesn't store any data from external systems. It just connects to them in realtime to fetch schemas.

## Read

Now that you've got a PostgreSQL database added, we can browse its structure.

```bash
recap ls
```

```
[
  "my-pg"
]
```

The `ls` command lists a path in Recap. Since we haven't supplied a path, it's listing systems. Let's keep drilling down:

```bash
recap ls my-pg
```

```
[
  "postgres",
  "template0",
  "template1",
  "testdb"
]
```

You can see the databases inside my PostgreSQL system. Recap models Postgres paths as `[system]/[database]/[schema]/[table]`, so the the `testdb` database is `my-pg/testdb`.

Browse around a bit to get a feel for how things are structured. Eventually, you'll find a path to a table. In my database, I've got `my-pg/testdb/public/test_types`. Let's read the schema:

```bash
recap schema my-pg/testdb/public/test_types
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

## Serve

We've been using Recap's CLI to read schemas, but Recap comes with an gateway server as well. The gateway server can list and read schemas over HTTP/JSON. You will find this handy if you're not using Python, or if you want to integrate Recap with other systems.

Start the server at [http://localhost:8000](http://localhost:8000):

```bash
recap serve
```

The server exposes `/ls` and `/schema` endpoints that are very similar to the CLI. I've already added a `my-pg` system in the [CLI](#cli) section above, so I can list the system in my Recap gateway:

```bash
$ curl http://localhost:8000/ls/my-pg
```

```json
["postgres","template0","template1","testdb"]
```

And much like the CLI, I can read my `test_types` schema:

```bash
curl http://localhost:8000/schema/my-pg/testdb/public/test_types
```

```json
{"type":"struct","fields":[{"type":"int64","name":"test_bigint","optional":true}]}
```

{: .note}
Recap's HTTP/JSON gateway does not require a database or any persistence. It just connects to external systems in realtime to fetch schemas.

## Next Steps

You've learned how to install Recap, connect it to a PostgreSQL database, read schemas, and start Recap's gateway server.

Next, you should look at Recap's [integrations](/docs/integrations) page to learn how to use Recap with other systems. If you're planning on running Recap's gateway, check out its configuration options in the [gateway](/docs/gateway) documentation. Finally, see Recap's [API](/docs/api) documentation to learn how to use Recap's Python API.