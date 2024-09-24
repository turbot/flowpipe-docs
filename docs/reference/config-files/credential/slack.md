---
title:  slack
sidebar_label: slack
---

# slack

> ***As of Flowpipe 0.10.0, `credential` has been renamed to `connection`.  The `slack` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [slack connection](/docs/reference/config-files/connection/slack) instead.***

The `slack` credential can be used to access Slack resources.

```hcl
credential "slack" "my_slack" {
  token = "xoxp-234567890"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`         |  String | Optional | An API token for slack.

All arguments are optional, and a `slack` credential with no arguments will behave the same as the [Slack default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`SLACK_TOKEN`)

## Default Credential

The Slack credential type includes an implicit, default credential (`credential.slack.default`) that will be configured to set the `token` to the `SLACK_TOKEN` environment variable.

```hcl
credential "slack" "default" {
  token = env("SLACK_TOKEN")
}
```

## Examples

### Static Credentials

```hcl
credential "slack" "my_slack" {
  token = "xoxp-234567890"
}
```

### Using Slack Credentials in HTTP Step

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
      Authorization = "Bearer ${credentials.slack[param.cred].token}"
    }

    request_body = jsonencode({
      types            = "public_channel"
    })
  }
}
```
