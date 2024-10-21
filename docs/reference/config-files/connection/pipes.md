---
title: pipes
sidebar_label: pipes
---

# pipes

The `pipes` connection can be used to access [Turbot Pipes](https://pipes.turbot.com/) resources.

```hcl
connection "pipes" "my_pipes" {
  token = "tpt_cld630jTisISAFakeTokenAdqizwE2OgVcS7N9UHb"
}
```

## Arguments

| Name    | Type   | Required? | Description |
| ------- | ------ | --------- | ----------- |
| `token` | String | Optional  | API token   |

All arguments are optional, and a `pipes` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                    |
| --------- | ---- | ------------------------------------------------------------------------------ |
| `env`     | Map  | A map of the resolved connection-related environment variables (`PIPES_TOKEN`) |

## Default Connection

The `pipes` connection type includes an implicit, default connection (`connection.pipes.default`) that will be configured to set the `token` to the `PIPES_TOKEN` environment variable.

```hcl
connection "pipes" "default" {
  token = env("PIPES_TOKEN")
}
```
