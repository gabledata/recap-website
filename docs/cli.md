---
title: CLI
layout: default
nav_order: 3
---

# Command Line Interface
{: .no_toc}

Interacting with Recap from the command line.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Introduction

Recap ships with a command line interface (CLI). You can use it to manage configuration, browse systems, read schemas, and start Recap's [gateway server](/docs/gateway).

## Commands

### `add`

Add a system to the config.

```
Usage: recap add [OPTIONS] SYSTEM URL
```

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| SYSTEM | TEXT | The name of the system. | | YES |
| URL | TEXT | URL for the system. | | YES |

### `ls`

List the children of a path.

```
Usage: recap ls [OPTIONS] [PATH]
```

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| PATH | TEXT | Path to list children of. | / | YES |

### `remove`

Remove a system from the config.

```
Usage: recap remove [OPTIONS] SYSTEM URL
```

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| SYSTEM | TEXT | The name of the system. | | YES |

### `schema`

Get the schema of a path.

```
Usage: recap schema [OPTIONS] [PATH]
```

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| PATH | TEXT | Path to get schema of. | / | YES |

### `serve`

Start Recap's HTTP/JSON gateway server. Uses [uvicorn](https://www.uvicorn.org/) under the hood.

```
Usage: recap serve [OPTIONS]
```

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| `--host` | TEXT | Hostname to bind the server to. | / | YES |
| `--port` | TEXT | Port to bind the server to. | 8000 | YES |
| `--log-level` | TEXT | Log level for the server. | info | YES |
