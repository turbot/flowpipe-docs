---
title:  okta
sidebar_label: okta
---

# okta

The `okta` credential can be used to access Okta resources.

```hcl
credential "okta" "my_okta" {
  domain    = "https://dev-11111111.okta.com"
  api_token = "00B..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_token`     |  String | Optional | API token
| `domain`        |  String | Optional | URL for your Okta Organization

All arguments are optional, and a `okta` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`OKTA_CLIENT_TOKEN`, `OKTA_ORGURL`)

## Default Credential

The `okta` credential type includes an implicit, default credential (`credential.okta.default`) that will be configured to set the `api_token` to the `OKTA_CLIENT_TOKEN` environment variable and the `domain` to the `OKTA_ORGURL` environment variable.

```hcl
credential "okta" "default" {
  api_token = env("OKTA_CLIENT_TOKEN")
  domain    = env("OKTA_ORGURL")
}
```
