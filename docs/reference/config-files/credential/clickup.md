---
title:  clickup
sidebar_label: clickup
---

# clickup

> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `clickup` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [clickup connection](/docs/reference/config-files/connection/clickup) instead.***

The `clickup` credential can be used to access ClickUp resources.

```hcl
credential "clickup" "my_clickup" {
  token = "pk_616_L5H36X3CXXXXXXXWEAZZF0NM5"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`         |  String | Optional | API token

All arguments are optional, and a `clickup` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `clickup` credential type includes an implicit, default credential (`credential.clickup.default`) that will be configured to set the `token` to the `CLICKUP_TOKEN` environment variable.

```hcl
credential "clickup" "default" {
  token = env("CLICKUP_TOKEN")
}
```
