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

Recap's HTTP/JSON server comes with a gateway API that you can use to query systems and schemas. The server doesn't require any database or storage. It's simply a gateway that connects to systems and returns Recap schemas.

## Usage

Use the `recap serve` command to start the server.

```bash
recap serve
```

See the [CLI](/docs/cli#serve) documentation for additional `serve` parameters and examples.

{: .note }
The gateway server is a simple [FastAPI](https://fastapi.tiangolo.com/) application. The `serve` command starts the FastAPI application using [uvicorn](https://www.uvicorn.org/). You may choose any ASGI-compatible server. See [FastAPI's documentation](https://fastapi.tiangolo.com/deployment/manually/) for more information.

You can query a system like so:

```bash
curl http://localhost:8000/gateway/ls/postgresql://user:pass@localhost:5432/testdb
```

And read a schema:

```bash
curl http://localhost:8000/gateway/schema/postgresql://user:pass@localhost:5432/testdb/public/test_types
```

## Configuration

The gateway will work out of the box without any configuration. Any URL with a compatible [client integration](/docs/integrations/) will work. However, you can configure Recap to save access credentials in an environment variable using the `RECAP_URLS` environment variable.

- `RECAP_URLS`: A JSON-encoded list of URLs to connect to.

See the [configuration](/docs/configuration) for more information on the `RECAP_URLS` environment variable.

You can also set environment variables in .env files or secrets files. See Recap's [configuration](/docs/configuration) documentation for more information.

## API

Recap's gateway server exposes a very simple HTTP/JSON API with two endpoints:

* `/gateway/ls/[url]` - List children of a URL.
* `/gateway/schema/[url]` - Get a schema for a URL.

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
    -e "RECAP_URLS='[\"postgresql://user:pass@pg:5432/testdb\"]'" \
    ghcr.io/recap-build/recap:latest
```

This command binds the server to port 8000 and connects to a PostgreSQL system named `pg`. See the [secrets](/docs/secrets) documentation if you wish to use secrets files instead for your connection strings.
