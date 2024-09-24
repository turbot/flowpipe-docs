---
title: datadog
sidebar_label: datadog
---

# datadog

The `datadog` connection can be used to access Datadog resources.

```hcl
connection "datadog" "my_datadog_connection" {
  api_key = "b1cf234................."
  app_key = "1a2345bc..................."
  api_url = "https://api.adoghq.com/"
}
```

## Arguments

| Name      | Type   | Required? | Description     |
| --------- | ------ | --------- | --------------- |
| `api_key` | String | Optional  | API key         |
| `app_key` | String | Optional  | Application key |
| `api_url` | String | Optional  | API URL         |

All arguments are optional, and a `datadog` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `datadog` connection type includes an implicit, default connection (`connection.datadog.default`) that will be configured using the environment variables `DD_CLIENT_API_KEY`, and `DD_CLIENT_APP_KEY`.

```hcl
connection "datadog" "my_datadog_connection" {
  api_key = env("DD_CLIENT_API_KEY")
  app_key = env("DD_CLIENT_APP_KEY")
}
```
