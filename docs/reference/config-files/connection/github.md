---
title:  github
sidebar_label: github
---

# github

The `github` connection can be used to access GitHub resources.

```hcl
connection "github" "my_gh_token" {
  token = "ghpat-..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`         |  String | Optional | API access token

All arguments are optional, and a `github` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved connection-related environment variables (`GITHUB_TOKEN`)

## Default Connection

The `github` connection type includes an implicit, default connection (`connection.github.default`) that will be configured to set the `token` to the `GITHUB_TOKEN` environment variable.

```hcl
connection "github" "default" {
  token = env("GITHUB_TOKEN")
}
```
