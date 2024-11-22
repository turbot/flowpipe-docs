---
title: Managing Workspaces
sidebar_label: Workspaces
---

# Managing Workspaces

A Tailpipe workspace is a profile that defines the environment in which Tailpipe operates.

Each workspace comprises:

- A single local workspace directory for Tailpipe data (parquet) and metadata files

<!--
- Optionally, a single remote object store for synchronizing tailpipe data (parquet) and metadata
-->

- Optionally, context-specific settings and options

You can use **workspaces** to keep independent sets of data. <!-- and sync them with independent remote libraries in S3 -->.  Tailpipe workspaces enable you to define multiple named configurations and easily switch among them using the `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable.  If there is no `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable, then the `default` workspace will be used.

Workspace configurations can be defined in any `.tpc` file in the `~/.tailpipe/config` directory, but by convention they are defined in `~/.tailpipe/config/workspaces.tpc` file. This file may contain multiple workspace definitions that can then be referenced by name.

```hcl
workspace "default" {
  local             = "~/.tailpipe/data/default"
  remote            = "s3://myorg-tailpipe-data/tailpipe/prod"
  remote_connection = connection.aws.default
}

workspace "dev" {
  local             = "~/.tailpipe/data/dev"
  remote            = "s3://myorg-tailpipe-data/tailpipe/dev"
  remote_connection = connection.aws.default
}

workspace "personal" {
  local             = "~/personal/tailpipe/data/default"
  remote            = "s3://my-bucket"
  remote_connection = connection.aws.personal
}

```



