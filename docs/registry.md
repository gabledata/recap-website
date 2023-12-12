---
title: Registry
layout: default
nav_order: 7
---

# Registry
{: .no_toc}

Running Recap's HTTP/JSON schema registry.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Overview

Recap comes with an HTTP/JSON server that you can use to store and retrieve Recap schemas (similar to [Confluent's Schema Registry](https://docs.confluent.io/platform/current/schema-registry/index.html) and [Buf's Schema Registry](https://buf.build/docs/bsr/introduction)). The registry is useful for storing schemas in a central location that can be accessed by multiple applications.

### Storage

The server stores schemas on a filesystem or object store like [S3](https://aws.amazon.com/s3/), [GCS](https://cloud.google.com/storage), or [ABS](https://azure.microsoft.com/en-us/services/storage/blobs/).

### Schemas

Schemas are identified using a unique string name. Names are stored as URL-quoted strings on disk, so you can use complex names and URLs like `snowflake://my-account/my_database/my_schema/my_table` as schema names.

### Versions

The registry server supports multiple versions of a schema. Versions are modeled as integers. The registry server will automatically assign a version to a schema when it is created (starting at 1 and automatically incrementing each subsequent version). You can also manually assign a version to a schema as well.

## Usage

Use the `recap serve` command to start the server.

```bash
recap serve
```

See the [CLI](/docs/cli#serve) documentation for additional `serve` parameters and examples.

{: .note }
The registry server is a simple [FastAPI](https://fastapi.tiangolo.com/) application. The `serve` command starts the FastAPI application using [uvicorn](https://www.uvicorn.org/). You may choose any ASGI-compatible server. See [FastAPI's documentation](https://fastapi.tiangolo.com/deployment/manually/) for more information.

## Configuration

The registry will work out of the box without any configuration. Schemas are stored in `$RECAP_HOME/schemas`. You can change the storage location by setting the following environment variables:

- `RECAP_REGISTRY_STORAGE_URL`: The URL to the storage location. Can be a local filesystem path or a URL to an object store like S3.
- `RECAP_REGISTRY_STORAGE_URL_ARGS`: Additional arguments to pass to the storage URL.

The registry is using [fsspec](https://filesystem-spec.readthedocs.io/en/latest/) to store schemas, so fsspec-compatible URLs will work. See the [fsspec implementations](https://filesystem-spec.readthedocs.io/en/latest/api.html#other-known-implementations) and [fsspec documentation](https://filesystem-spec.readthedocs.io/en/latest/usage.html) for more information.

Here's an example of using S3:

```bash
export RECAP_REGISTRY_STORAGE_URL=s3://my-bucket/schemas
```

{: .note }
Make sure to install the fsspec implementation for your storage URL. For example, you'll need to install [s3fs](https://s3fs.readthedocs.io/en/latest/) to use S3.

After running `recap serve`, you can add schemas to the registry:

```bash
curl -X POST \
    -H "Content-Type: application/x-recap+json" \
    -d '{"type":"struct","fields":[{"type":"int64","name":"test_bigint","optional":true}]}' \
    http://localhost:8000/registry/some_schema
```

You can also set environment variables in .env files or secrets files. See Recap's [configuration](/docs/configuration) documentation for more information.

## API

Recap's registry server exposes a simple HTTP/JSON API with the following endpoints:

* `[GET]` `/registry` - List all schemas in the registry.
* `[GET]` `/registry/[name]` - Get a schema by name.
* `[GET]` `/registry/[name]/versions` - List all versions of a schema.
* `[GET]` `/registry/[name]/versions/[version]` - Get a schema by name and version.
* `[POST]` `/registry/[name]` - Create a new schema. The version will be the highest version of the schema plus one.
* `[PUT]` `/registry/[name]/versions/[version]` - Put a schema by name and version.

### OpanAPI

An [OpenAPI](https://www.openapis.org/) schema is available at [http://localhost:8000/openapi.json](http://localhost:8000/openapi.json).

### Swagger

A Swagger UI is available at [http://localhost:8000/docs](http://localhost:8000/docs).

### Redoc

A Redoc UI is available at [http://localhost:8000/redoc](http://localhost:8000/redoc).

## Docker

Recap's registry server is available as a Docker image at [ghcr.io/recap-build/recap:latest](https://github.com/recap-build/recap/pkgs/container/recap).

```bash
docker pull ghcr.io/recap-build/recap:latest
```

You can run the server using the `docker run` command:

```bash
docker run \
    -p 8000:8000 \
    -e "RECAP_REGISTRY_STORAGE_URL=s3://my-bucket/recap/schemas" \
    ghcr.io/recap-build/recap:latest
```

This command binds the server to port 8000. See the [secrets](/docs/secrets) documentation if you wish to use secrets files for your connection strings.
