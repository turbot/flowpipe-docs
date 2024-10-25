---
title:  uptimerobot
sidebar_label: uptimerobot
---

# uptimerobot

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `uptimerobot` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [uptimerobot connection](/docs/reference/config-files/connection/uptimerobot) instead.***

The `uptimerobot` credential can be used to access UptimeRobot resources.

```hcl
credential "uptimerobot" "my_uptimerobot" {
  api_key = "u123456-ecaf32fakekey633ff33dd3c445"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API Key

All arguments are optional, and a `uptimerobot` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`UPTIMEROBOT_API_KEY`)

## Default Credential

The `uptimerobot` credential type includes an implicit, default credential (`credential.uptimerobot.default`) that will be configured to set the `api_key` to the `UPTIMEROBOT_API_KEY` environment variable.

```hcl
credential "uptimerobot" "default" {
  api_key = env("UPTIMEROBOT_API_KEY")
}
```
