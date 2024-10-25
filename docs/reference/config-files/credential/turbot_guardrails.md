---
title:  guardrails
sidebar_label: guardrails
---

# guardrails

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `guardrails` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [guardrails connection](/docs/reference/config-files/connection/guardrails) instead.***

The `guardrails` credential can be used to access [Turbot Guardrails](https://turbot.com/guardrails) resources.

```hcl
credential "guardrails" "my_guardrail" {
  access_key = "c8e2c2ed-1ca8-429b-b369-123..."
  secret_key = "a3d8385d-47f7-40c5-a90c-123..."
  workspace = "https://my_workspace.sass.turbot.com"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `access_key`    |  String | Optional | Access key
| `secret_key`    |  String | Optional | Secret key
| `workspace`     |  String | Optional | Workspace URL

All arguments are optional, and a `guardrails` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`TURBOT_ACCESS_KEY`, `TURBOT_SECRET_KEY`, `TURBOT_WORKSPACE`)

## Default Credential

The `guardrails` credential type includes an implicit, default credential (`credential.guardrails.default`) that will be configured using the environment variables `TURBOT_ACCESS_KEY`, `TURBOT_SECRET_KEY`, and `TURBOT_WORKSPACE`.

```hcl
credential "guardrails" "my_guardrail" {
  access_key = env("TURBOT_ACCESS_KEY")
  secret_key = env("TURBOT_SECRET_KEY")
  workspace  = env("TURBOT_WORKSPACE")
}
```
