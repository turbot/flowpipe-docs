---
title: bitbucket
sidebar_label: bitbucket
---

# bitbucket

The `bitbucket` connection can be used to access Bitbucket resources.

```hcl
connection "bitbucket" "bb_connection" {
  username = "user_name"
  password = "P@ss123!"
  base_url = "https://api.bitbucket.org/2.0"
}
```

## Arguments

| Name       | Type   | Required? | Description                       |
| ---------- | ------ | --------- | --------------------------------- |
| `username` | String | Optional  | Bitbucket username                |
| `password` | String | Optional  | Bitbucket app password            |
| `base_url` | String | Optional  | Base URL of your Bitbucket Server |

All arguments are optional, and a `bitbucket` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                                                                           |
| --------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`BITBUCKET_USERNAME`, `BITBUCKET_PASSWORD`, `BITBUCKET_API_BASE_URL`) |

## Default Connection

The `bitbucket` connection type includes an implicit, default connection (`connection.bitbucket.default`) that will be configured using the environment variables `BITBUCKET_USERNAME`, `BITBUCKET_PASSWORD`, and `BITBUCKET_API_BASE_URL`.

```hcl
connection "bitbucket" "default" {
  username = env("BITBUCKET_USERNAME")
  password = env("BITBUCKET_PASSWORD")
  base_url = env("BITBUCKET_API_BASE_URL")
}
```
