---
title: slack
sidebar_label: slack
---

# slack

The `slack` connection can be used to access Slack resources.

```hcl
connection "slack" "my_slack" {
  token = "xoxp-234567890"
}
```

## Arguments

| Name    | Type   | Required? | Description             |
| ------- | ------ | --------- | ----------------------- |
| `token` | String | Optional  | An API token for slack. |

All arguments are optional, and a `slack` connection with no arguments will behave the same as the [Slack default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                    |
| --------- | ---- | ------------------------------------------------------------------------------ |
| `env`     | Map  | A map of the resolved connection-related environment variables (`SLACK_TOKEN`) |

## Default Connection

The Slack connection type includes an implicit, default connection (`connection.slack.default`) that will be configured to set the `token` to the `SLACK_TOKEN` environment variable.

```hcl
connection "slack" "default" {
  token = env("SLACK_TOKEN")
}
```

## Examples

### Static Connections

```hcl
connection "slack" "my_slack" {
  token = "xoxp-234567890"
}
```

### Using Slack Connections in HTTP Step

<!-- NOTE: Waiting on confirmation -->

```hcl
pipeline "gcp_test" {
  param "cred" {
    type    = string
    default = "default"
  }

  step "http" "list_channels" {
    url    = "https://slack.com/api/conversations.list"
    method = "get"

    request_headers = {
      Content-Type  = "application/json; charset=utf-8"
      Authorization = "Bearer ${connections.slack[param.cred].token}"
    }

    request_body = jsonencode({
      types            = "public_channel"
    })
  }
}
```
