---
title: Configuration
layout: default
nav_order: 5
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
| RECAP_URLS | TEXT| List of URLs in JSON format containing authorization credentials. | | NO |
| RECAP_REGISTRY_STORAGE_URL | TEXT | Location to store registry schemas in an [`fsspec`-compatible URL format](https://filesystem-spec.readthedocs.io/en/latest/api.html#other-known-implementations). | | NO |
| RECAP_REGISTRY_STORAGE_ARGS | TEXT | Additional attributes to supply when creating the registry's `fsspec` filesystem object. | | NO |

#### RECAP_URLS

The `RECAP_URLS` environment variable is important. It allows you to define connection strings for URLs that contain login credentials like usernames and passwords or authentication tokens.

For example, if you have a URL like `postgresql://user:pass@localhost:5432/testdb`, you can set the `RECAP_URLS` environment variable like so:

```bash
export RECAP_URLS='["postgresql://some_user:some_pass@localhost:5432/testdb"]'
```

All `ls` or `schema` calls for the URL will use the credentials defined in the environment variable.

You don't need to specify a full path for URL credentials. Recap will substitute in any username, password, or query parameters that are defined in the environment variable, so this command:

```bash
curl http://localhost:8000/gateway/ls/postgresql://localhost:5432/testdb/public
```

Would be translated to:

```bash
curl http://localhost:8000/gateway/ls/postgresql://some_user:some_pass@localhost:5432/testdb/public
```

{: .note }
The URL matching is done on the scheme ("postgresql" in our example) and netloc ("localhost:5432" in our example) for a URL.

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

For example, if Recap is configured to use `/path/to/secrets` as its secrets directory, and `/path/to/secrets/recap_urls` contains `["postgresql://user:passlocalhost:5432/testdb"]`, then Recap will use the credentials defined in the file for any `ls` or `schema` calls for the URL.

See [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings#secrets)'s secrets section for more information.

### Docker

If you're deploying Recap's server in Docker, see the [gateway](/docs/gateway#docker) and [registry](/docs/registry#docker) documentation and [pydantic-settings](https://docs.pydantic.dev/latest/usage/pydantic_settings/#use-case-docker-secrets) for Docker secrets instructions.