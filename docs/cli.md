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

Recap ships with a command line interface (CLI). You can use it to manage configuration, browse systems, read schemas, and start Recap's [gateway server](/docs/gateway).

## Commands

### `add`

Add a system to the config.

#### Usage
{: .no_toc }

```
recap add [OPTIONS] SYSTEM URL
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| SYSTEM | TEXT | The name of the system. | | YES |
| URL | TEXT | URL for the system. | | YES |

#### Example
{: .no_toc }

```
recap add my_pg postgresql://user:pass@host:port/dbname
```

### `ls`

List the children of a path.

#### Usage
{: .no_toc }

```
recap ls [OPTIONS] [PATH]
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| PATH | TEXT | Path to list children of. | / | YES |

#### Example
{: .no_toc }

```
recap ls my_pg/testdb
```

### `remove`

Remove a system from the config.

#### Usage
{: .no_toc }

```
recap remove [OPTIONS] SYSTEM URL
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| SYSTEM | TEXT | The name of the system. | | YES |

#### Example
{: .no_toc }

```
recap remove my_pg
```

### `schema`

Get the schema of a path.

#### Usage
{: .no_toc }

```
recap schema [OPTIONS] [PATH]
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| PATH | TEXT | Path to get schema of. | / | YES |

#### Example
{: .no_toc }

```
recap schema my_pg/testdb/public/my_table
```

### `serve`

Start Recap's HTTP/JSON gateway server. Uses [uvicorn](https://www.uvicorn.org/) under the hood.

#### Usage
{: .no_toc }

```
recap serve [OPTIONS]
```

#### Parameters
{: .no_toc }

| Option | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| `--host` | TEXT | Hostname to bind the server to. | / | YES |
| `--port` | TEXT | Port to bind the server to. | 8000 | YES |
| `--log-level` | TEXT | Log level for the server. | info | YES |

#### Example
{: .no_toc }

```
recap serve
```
