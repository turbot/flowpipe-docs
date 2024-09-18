---
title:  teams
sidebar_label: teams
---

# teams

> ***As of Flowpipe 0.10.0, `credential` has been renamed to `connection`.  The `teams` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [teams connection](/docs/reference/config-files/connection/teams) instead.***

The `teams` credential can be used to access Microsoft Teams resources.

```hcl
credential "teams" "my_teams" {
  access_token= "bfc6f1c4fakesdfdxxxx26fake977b2xxxsfsdfakef313c3d389126de0d"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `access_token`  |  String | Optional | API token

All arguments are optional, and a `teams` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`TEAMS_ACCESS_TOKEN`)

## Default Credential

The `teams` credential type includes an implicit, default credential (`credential.teams.default`) that will be configured to set the `access_token` to the `TEAMS_ACCESS_TOKEN` environment variable.

```hcl
credential "teams" "default" {
  access_token = env("TEAMS_ACCESS_TOKEN")
}
```
