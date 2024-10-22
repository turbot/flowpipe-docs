---
title: pagerduty
sidebar_label: pagerduty
---

# pagerduty

The `pagerduty` connection can be used to access PagerDuty resources.

```hcl
connection "pagerduty" "my_pagerduty" {
  token = "u+_szhLR4FakeTokenGZSA"
}
```

## Arguments

| Name    | Type   | Required? | Description |
| ------- | ------ | --------- | ----------- |
| `token` | String | Optional  | API token   |

All arguments are optional, and a `pagerduty` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `pagerduty` connection type includes an implicit, default connection (`connection.pagerduty.default`) that will be configured to set the `token` to the `PAGERDUTY_TOKEN` environment variable.

```hcl
connection "pagerduty" "default" {
  token = env("PAGERDUTY_TOKEN")
}
```
