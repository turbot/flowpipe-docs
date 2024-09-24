---
title:  clickup
sidebar_label: clickup
---

# clickup

The `clickup` connection can be used to access ClickUp resources.

```hcl
connection "clickup" "my_clickup" {
  token = "pk_616_L5H36X3CXXXXXXXWEAZZF0NM5"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`         |  String | Optional | API token

All arguments are optional, and a `clickup` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `clickup` connection type includes an implicit, default connection (`connection.clickup.default`) that will be configured to set the `token` to the `CLICKUP_TOKEN` environment variable.

```hcl
connection "clickup" "default" {
  token = env("CLICKUP_TOKEN")
}
```
