---
title: Concepts
layout: default
nav_order: 3
---

# Concepts
{: .no_toc}

Understanding Recap's design.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Overview

You should understand the following concepts before using Recap.

## Systems

Systems are Recap's term for remote schema sources. They're typically databases, schema registries, data catalogs, and so on.

Each system has a name and a URL. Systems are normally defined as environment variables.

```bash
export MY_PG=postgresql://user:pass@host:port/dbname
```

See Recap's [configuration](/docs/configuration) documentation for more information.

## Paths

Recap takes a path as an argument when listing or reading schemas. Each system has its own path structure. For example, PostgreSQL has paths like:

```
[system]/[database]/[schema]/[table]
```

You can browse paths using the `ls` command:

```bash
recap ls my_pg
recap ls my_pg/testdb
recap ls my_pg/testdb/public
```

{: .note }
This example uses Recap's [CLI](/docs/cli). You can also use Recap's [Python API](/docs/python) or [gateway](/docs/gateway).

## Clients

Each system has a [client](/docs/python#clients) implementation. Clients connect to remote systems, read schemas, and convert them to Recap schemas. Clients use converters to convert to Recap schemas.

## Converters

Converters are used to convert to and from Recap schemas. Unlike clients, converters do not connect to remote systems. They just take an in-memory schema and return a Recap schema (or vice-versa).

## CLI

Recap's [CLI](/docs/cli) is a command line interface for connecting to systems and reading schemas. It's a thin wrapper around Recap's Python API.

## Gateway

Recap's [gateway](/docs/gateway) is a stateless HTTP/JSON server that you can use to query systems and schemas. The server doesn't require any database or storage. It's simply connects to systems and returns Recap schemas.

The gateway is useful if you're not using Python, but want to use Recap programmatically.

## Python API

Recap is written in Python and is designed to be used as a Python library. See the [Python](/docs/python) documentation for more information.