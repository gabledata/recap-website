---
title: Gateway
layout: default
nav_order: 6
---

# Gateway
{: .no_toc}

Running Recap's stateless HTTP/JSON gateway server.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Overview

Recap comes with a stateless HTTP/JSON server that you can use to query systems and schemas. The server doesn't require any database or storage. It's a simple gateway that connects to systems and returns Recap schemas.

## Usage

Use the `recap serve` command to start the server.

```bash
recap serve
```

See the [CLI](/docs/cli#serve) documentation for additional `serve` parameters and examples.

{: .note }
The gateway server is a simple [FastAPI](https://fastapi.tiangolo.com/) application. The `serve` command starts the FastAPI application using [uvicorn](https://www.uvicorn.org/). You may choose any ASGI-compatible server. See [FastAPI's documentation](https://fastapi.tiangolo.com/deployment/manually/) for more information.

## Configuration

You must define Recap "system" connections as environment variables before starting the gateway server. Recap uses these environment variables to connect to systems.

Here's an example for a PostgreSQL system:

```bash
export MY_PG=postgresql://user:pass@host:port/dbname
```

Or you can use Recap's CLI:

```bash
recap add my_pg postgresql://user:pass@host:port/dbname
```

After running `recap serve`, you can query the system using the `my_pg` name:

```bash
curl http://localhost:8000/ls/my_pg
```

You can also set environment variables in .env files or secrets files. See Recap's [configuration](/docs/configuration) documentation for more information.

## API

Recap's gateway server exposes a very simple HTTP/JSON API with two endpoints:

* `/ls/[path*]` - List children in a system path.
* `/schema/[path*]` - Get a schema for a system path.

### OpanAPI

An [OpenAPI](https://www.openapis.org/) schema is available at [http://localhost:8000/openapi.json](http://localhost:8000/openapi.json).

### Swagger

A Swagger UI is available at [http://localhost:8000/docs](http://localhost:8000/docs).

### Redoc

A Redoc UI is available at [http://localhost:8000/redoc](http://localhost:8000/redoc).

## Docker

Recap's gateway server is available as a Docker image at [ghcr.io/recap-build/recap:latest](https://github.com/recap-build/recap/pkgs/container/recap).

```bash
docker pull ghcr.io/recap-build/recap:latest
```

You can run the server using the `docker run` command:

```bash
docker run \
    -p 8000:8000 \
    -e "RECAP_SYSTEMS__PG=postgresql://user:pass@localhost:5432/testdb" \
    ghcr.io/recap-build/recap:latest
```

This command binds the server to port 8000 and connects to a PostgreSQL system named `pg`. See the [secrets](/docs/secrets) documentation if you wish to use secrets files instead for your connection strings.
