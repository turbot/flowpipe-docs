---
title: urlscan
sidebar_label: urlscan
---

# urlscan

The `urlscan` connection can be used to access URLScan resources.

```hcl
connection "urlscan" "my_urlscan" {
  api_key = "11111111-e127-1234-adcd-59cad2f12abc"
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API Key     |

All arguments are optional, and a `urlscan` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                        |
| --------- | ---- | ---------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`URLSCAN_API_KEY`) |

## Default Connection

The `urlscan` connection type includes an implicit, default connection (`connection.urlscan.default`) that will be configured to set the `api_key` to the `URLSCAN_API_KEY` environment variable.

```hcl
connection "urlscan" "default" {
  api_key = env("URLSCAN_API_KEY")
}
```
