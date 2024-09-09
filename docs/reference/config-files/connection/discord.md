---
title: discord
sidebar_label: discord
---

# discord

The `discord` connection can be used to access Discord resources.

```hcl
connection "discord" "my_discord" {
  token = "00B630jSCGU4Fake5Yh4KQMFakezwE2OgVcS7N999b"
}
```

## Arguments

| Name    | Type   | Required? | Description |
| ------- | ------ | --------- | ----------- |
| `token` | String | Optional  | API token   |

All arguments are optional, and a `discord` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                      |
| --------- | ---- | -------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`DISCORD_TOKEN`) |

## Default Connection

The `discord` connection type includes an implicit, default connection (`connection.discord.default`) that will be configured to set the `token` to the `DISCORD_TOKEN` environment variable.

```hcl
connection "discord" "default" {
  token = env("DISCORD_TOKEN")
}
```
