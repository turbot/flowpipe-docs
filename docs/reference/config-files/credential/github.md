---
title:  github
sidebar_label: github
---

# github

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `github` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [github connection](/docs/reference/config-files/connection/github) instead.***

The `github` credential can be used to access GitHub resources.

```hcl
credential "github" "my_gh_token" {
  token = "ghpat-..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`         |  String | Optional | API access token

All arguments are optional, and a `github` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`GITHUB_TOKEN`)

## Default Credential

The `github` credential type includes an implicit, default credential (`credential.github.default`) that will be configured to set the `token` to the `GITHUB_TOKEN` environment variable.

```hcl
credential "github" "default" {
  token = env("GITHUB_TOKEN")
}
```
