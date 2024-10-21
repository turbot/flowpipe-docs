---
title:  bitbucket
sidebar_label: bitbucket
---

# bitbucket

> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `bitbucket` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [bitbucket connection](/docs/reference/config-files/connection/bitbucket) instead.***

The `bitbucket` credential can be used to access Bitbucket resources.

```hcl
credential "bitbucket" "bb_creds" {
  username = "user_name"
  password = "P@ss123!"
  base_url = "https://api.bitbucket.org/2.0"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `username`      |  String | Optional | Bitbucket username
| `password`      |  String | Optional | Bitbucket app password
| `base_url`      |  String | Optional | Base URL of your Bitbucket Server

All arguments are optional, and a `bitbucket` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`BITBUCKET_USERNAME`, `BITBUCKET_PASSWORD`, `BITBUCKET_API_BASE_URL`)

## Default Credential

The `bitbucket` credential type includes an implicit, default credential (`credential.bitbucket.default`) that will be configured using the environment variables `BITBUCKET_USERNAME`, `BITBUCKET_PASSWORD`, and `BITBUCKET_API_BASE_URL`.

```hcl
credential "bitbucket" "default" {
  username = env("BITBUCKET_USERNAME")
  password = env("BITBUCKET_PASSWORD")
  base_url = env("BITBUCKET_API_BASE_URL")
}
```
