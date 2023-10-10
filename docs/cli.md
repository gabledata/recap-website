---
title: CLI
layout: default
nav_order: 4
---

# Command Line Interface
{: .no_toc}

Interacting with Recap from the command line.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Introduction

Recap ships with a command line interface (CLI). You can use it to manage configuration, browse systems, read schemas, and start Recap's [gateway](/docs/gateway) and [registry](/docs/registry).

## Commands

### `ls`

List the children of a URL.

#### Usage
{: .no_toc }

```
recap ls [OPTIONS] [URL]
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default |
| :-- | :-- | :-- | :-: |
| URL | TEXT | URL to parent. | - |

#### Example
{: .no_toc }

```
recap ls postgres://user:pass@localhost:5432/testdb
```

### `schema`

Get the schema of a URL.

#### Usage
{: .no_toc }

```
recap schema [OPTIONS] URL
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default |
| :-- | :-- | :-- | :-: |
| URL | TEXT | URL to schema. | - |
| \-\-output-format  -of | TEXT | Schema output format. `[avro|json|protobuf|recap]` | recap |

#### Example
{: .no_toc }

```
recap schema postgres://user:pass@localhost:5432/testdb/public/test_types
```

### `serve`

Start Recap's HTTP/JSON server.

#### Usage
{: .no_toc }

```
recap serve [OPTIONS]
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default |
| :-- | :-- | :-- | :-: |
| `--host` | TEXT | Hostname to bind the server to. | 127.0.0.1 |
| `--port` | TEXT | Port to bind the server to. | 8000 |
| `--log-level` | TEXT | Log level for the server. | info |

#### Example
{: .no_toc }

```
recap serve
```
