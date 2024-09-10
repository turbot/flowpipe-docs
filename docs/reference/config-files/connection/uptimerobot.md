---
title: uptimerobot
sidebar_label: uptimerobot
---

# uptimerobot

The `uptimerobot` connection can be used to access UptimeRobot resources.

```hcl
connection "uptimerobot" "my_uptimerobot" {
  api_key = "u123456-ecaf32fakekey633ff33dd3c445"
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API Key     |

All arguments are optional, and a `uptimerobot` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                            |
| --------- | ---- | -------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`UPTIMEROBOT_API_KEY`) |

## Default Connection

The `uptimerobot` connection type includes an implicit, default connection (`connection.uptimerobot.default`) that will be configured to set the `api_key` to the `UPTIMEROBOT_API_KEY` environment variable.

```hcl
connection "uptimerobot" "default" {
  api_key = env("UPTIMEROBOT_API_KEY")
}
```
