---
title: ip2locationio
sidebar_label: ip2locationio
---

# ip2locationio

The `ip2locationio` connection can be used to access Ip2Location.io resources.

```hcl
connection "ip2locationio" "my_api_key" {
  api_key = "12345678901A23BC4D5E6FG78HI9J101"
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API key     |

All arguments are optional, and a `ip2locationio` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `ip2locationio` connection type includes an implicit, default connection (`connection.ip2locationio.default`) that will be configured to set the `api_key` to the `IP2LOCATIONIO_API_KEY` environment variable.

```hcl
connection "ip2locationio" "default" {
  api_key = env("IP2LOCATIONIO_API_KEY")
}
```
