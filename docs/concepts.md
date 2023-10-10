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

## Clients

Each system has a [client](/docs/python#clients) implementation. Clients connect to remote systems, read schemas, and convert them to Recap schemas. Clients use converters to convert to Recap schemas.

## Converters

Converters are used to convert to and from Recap schemas. Unlike clients, converters do not connect to remote systems. They just take an in-memory schema and return a Recap schema (or vice-versa).

## CLI

Recap's [CLI](/docs/cli) is a command line interface for connecting to systems and reading schemas. It's a thin wrapper around Recap's Python API.

## Gateway

Recap's [gateway](/docs/gateway) is a stateless HTTP/JSON API that you can use to query systems and schemas. The server doesn't require any database or storage. It's simply connects to systems and returns Recap schemas.

The gateway is useful if you're not using Python, but want to use Recap programmatically.

## Registry

Recap's [registry](/docs/registry) is an HTTP/JSON API that stores schemas in a filesystem or object store like S3 or GCS. The registry is useful for caching schemas or acting as a repository when using Recap schemas as a source of truth.

## Python API

Recap is written in Python and is designed to be used as a Python library. See the [Python](/docs/python) documentation for more information.