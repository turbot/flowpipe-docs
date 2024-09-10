---
title: jumpcloud
sidebar_label: jumpcloud
---

# jumpcloud

The `jumpcloud` connection can be used to access JumpCloud resources.

```hcl
connection "jumpcloud" "jumpcloud_api_key" {
  api_key = "2e256f0faked412345676fake914cf7e68d8e53c8d"
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API Key     |

All arguments are optional, and an `jumpcloud` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `jumpcloud` connection type includes an implicit, default connection (`connection.jumpcloud.default`) that will be configured to set the `api_key` to the `JUMPCLOUD_API_KEY` environment variable.

```hcl
connection "jumpcloud" "default" {
  api_key = env("JUMPCLOUD_API_KEY")
}
```
