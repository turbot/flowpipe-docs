---
title: guardrails
sidebar_label: guardrails
---

# guardrails

The `guardrails` connection can be used to access [Turbot Guardrails](https://turbot.com/guardrails) resources.

```hcl
connection "guardrails" "my_guardrail" {
  access_key = "c8e2c2ed-1ca8-429b-b369-123..."
  secret_key = "a3d8385d-47f7-40c5-a90c-123..."
  workspace = "https://my_workspace.sass.turbot.com"
}
```

## Arguments

| Name         | Type   | Required? | Description   |
| ------------ | ------ | --------- | ------------- |
| `access_key` | String | Optional  | Access key    |
| `secret_key` | String | Optional  | Secret key    |
| `workspace`  | String | Optional  | Workspace URL |

All arguments are optional, and a `guardrails` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                                                                   |
| --------- | ---- | ----------------------------------------------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`TURBOT_ACCESS_KEY`, `TURBOT_SECRET_KEY`, `TURBOT_WORKSPACE`) |

## Default Connection

The `guardrails` connection type includes an implicit, default connection (`connection.guardrails.default`) that will be configured using the environment variables `TURBOT_ACCESS_KEY`, `TURBOT_SECRET_KEY`, and `TURBOT_WORKSPACE`.

```hcl
connection "guardrails" "my_guardrail" {
  access_key = env("TURBOT_ACCESS_KEY")
  secret_key = env("TURBOT_SECRET_KEY")
  workspace  = env("TURBOT_WORKSPACE")
}
```
