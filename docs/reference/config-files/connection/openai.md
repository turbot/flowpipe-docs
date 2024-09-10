---
title:  openai
sidebar_label: openai
---

# openai

The `openai` connection can be used to access OpenAI resources.

```hcl
connection "openai" "my_openai" {
  api_key = "sk-jwgthNa..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API key

All arguments are optional, and an `openai` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved connection-related environment variables (`OPENAI_API_KEY`)

## Default Connection

The `openai` connection type includes an implicit, default connection (`connection.openai.default`) that will be configured to set the `api_key` to the `OPENAI_API_KEY` environment variable.

```hcl
connection "openai" "default" {
  api_key = env("OPENAI_API_KEY")
}
```
