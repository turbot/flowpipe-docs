---
title: sendgrid
sidebar_label: sendgrid
---

# sendgrid

The `sendgrid` connection can be used to access SendGrid resources.

```hcl
connection "sendgrid" "my_sendgrid" {
  api_key = "SG.R7..."
}
```

## Arguments

| Name      | Type   | Required? | Description      |
| --------- | ------ | --------- | ---------------- |
| `api_key` | String | Optional  | SendGrid API Key |

All arguments are optional, and a `sendgrid` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                         |
| --------- | ---- | ----------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`SENDGRID_API_KEY`) |

## Default Connection

The `sendgrid` connection type includes an implicit, default connection (`connection.sendgrid.default`) that will be configured to set the `api_key` to the `SENDGRID_API_KEY` environment variable.

```hcl
connection "sendgrid" "default" {
  api_key = env("SENDGRID_API_KEY")
}
```
