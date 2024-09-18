---
title: teams
sidebar_label: teams
---

# teams

The `teams` connection can be used to access Microsoft Teams resources.

```hcl
connection "teams" "my_teams" {
  access_token= "bfc6f1c4fakesdfdxxxx26fake977b2xxxsfsdfakef313c3d389126de0d"
}
```

## Arguments

| Name           | Type   | Required? | Description |
| -------------- | ------ | --------- | ----------- |
| `access_token` | String | Optional  | API token   |

All arguments are optional, and a `teams` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                           |
| --------- | ---- | ------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`TEAMS_ACCESS_TOKEN`) |

## Default Connection

The `teams` connection type includes an implicit, default connection (`connection.teams.default`) that will be configured to set the `access_token` to the `TEAMS_ACCESS_TOKEN` environment variable.

```hcl
connection "teams" "default" {
  access_token = env("TEAMS_ACCESS_TOKEN")
}
```
