---
title: Configuration
layout: default
nav_order: 4
---

# Configuration
{: .no_toc}

Configuring Recap through the CLI or environment variables.
{: .fs-6 .fw-300}

1. TOC
{:toc}

## Overview

Recap's [CLI](/docs/cli) and [gateway](/docs/gateway) are configured through a combination of environment variables, config files, and a secrets directory.

The resolution order (lowest to highest priority):

1. Secrets file
2. Config file
3. Dotenv file
4. Environment variables

Recap uses [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings) to manage configuration. See its documentation for more information.

### Environment Variables

| Name | Type | Description | Default | Required |
| :-- | :-- | :-- | :-: | :-: |
| RECAP_CONFIG | TEXT | Path to the config file. | ~/.recap/config | YES |
| RECAP_SECRETS | TEXT | Path to the secrets directory. | | NO |
| RECAP_SYSTEMS__[SYSTEM] | TEXT | A system name/URL pair that tells Recap how to connect to a system. | | NO |

{: .note }
The `RECAP_SYSTEMS__` environment variable is a special case. It's used to configure systems. The `SYSTEM` part of the variable name is the name of the system. The value is the URL to connect to the system. For example, `RECAP_SYSTEMS__MY_PG=postgresql://user:pass@host:port/dbname` will configure a system called `my_pg` to connect to a PostgreSQL database.

## CLI

Recap's CLI has [`add`](/docs/cli#add) and [`remove`](/docs/cli#remove) commands to add and remove systems from Recap's config file (`RECAP_CONFIG`). These commands are just shortcuts for editing the config file directly.

### Add a System

```bash
recap add my_pg postgresql://user:pass@host:port/dbname
```

### Remove a System

```bash
recap remove my_pg
```

## Dotenv

Recap supports [`.env`](https://github.com/motdotla/dotenv) files as well. Recap will look for a `.env` file in the current working directory and load it if it exists. If not, it'll recurse upwards until it finds one.

Here's an example .env file:

```bash
RECAP_SECRETS=/path/to/secrets
RECAP_SYSTEMS__MY_BQ=bigquery://
RECAP_SYSTEMS__MY_PG=postgresql://localhost:5432/testdb
```

See [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings/#dotenv-env-support) for more information.

## Secrets

Recap supports a secrets directory (`RECAP_SECRETS`) that contains files with secrets. Each file's name corrseponds to its environment variable name, and each file's contents are the environment variable's value.

For example, if Recap is configured to use `/path/to/secrets` as its secrets directory, and `/path/to/secrets/recap_systems__my_pg` contains `postgresql://localhost:5432/testdb`, then Recap will see `RECAP_SYSTEMS__MY_PG`'s value as `postgresql://localhost:5432/testdb`.

{: .warning }
> If you want to set more than one system, you'll need to use the secret filename `recap_systems`. The contents should be a JSON-encoded map from system name to system URL. For example:
> 
> ```json
> {
>   "my_bq": "bigquery://",
>   "my_pg": "postgresql://localhost:5432/testdb"
> }
> ```

See [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings#secrets)'s secrets section for more information.

### Docker

If you're deploying Recap's [gateway](/docs/gateway) in Docker, see the [Gateway Docker](/docs/gateway#docker) documentation and [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings/#use-case-docker-secrets) for Docker secrets instructions.