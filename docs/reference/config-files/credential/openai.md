---
title:  openai
sidebar_label: openai
---

# openai

> ***As of Flowpipe 0.10.0, `credential` has been renamed to `connection`.  The `openai` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [openai connection](/docs/reference/config-files/connection/openai) instead.***

The `openai` credential can be used to access OpenAI resources.

```hcl
credential "openai" "my_openai" {
  api_key = "sk-jwgthNa..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API key

All arguments are optional, and an `openai` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`OPENAI_API_KEY`)

## Default Credential

The `openai` credential type includes an implicit, default credential (`credential.openai.default`) that will be configured to set the `api_key` to the `OPENAI_API_KEY` environment variable.

```hcl
credential "openai" "default" {
  api_key = env("OPENAI_API_KEY")
}
```
