---
title:  okta
sidebar_label: okta
---

# okta

The `okta` connection can be used to access Okta resources.

```hcl
connection "okta" "my_okta" {
  domain    = "https://dev-11111111.okta.com"
  api_token = "00B..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_token`     |  String | Optional | API token
| `domain`        |  String | Optional | URL for your Okta Organization

All arguments are optional, and a `okta` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved connection-related environment variables (`OKTA_CLIENT_TOKEN`, `OKTA_ORGURL`)

## Default Connection

The `okta` connection type includes an implicit, default connection (`connection.okta.default`) that will be configured to set the `api_token` to the `OKTA_CLIENT_TOKEN` environment variable and the `domain` to the `OKTA_ORGURL` environment variable.

```hcl
connection "okta" "default" {
  api_token = env("OKTA_CLIENT_TOKEN")
  domain    = env("OKTA_ORGURL")
}
```
