---
title:  datadog
sidebar_label: datadog
---

# datadog

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `datadog` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [datadog connection](/docs/reference/config-files/connection/datadog) instead.***


The `datadog` credential can be used to access Datadog resources.

```hcl
credential "datadog" "my_datadog_creds" {
  api_key = "b1cf234................."
  app_key = "1a2345bc..................."
  api_url = "https://api.adoghq.com/"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API key
| `app_key`       |  String | Optional | Application key
| `api_url`       |  String | Optional | API URL

All arguments are optional, and a `datadog` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `datadog` credential type includes an implicit, default credential (`credential.datadog.default`) that will be configured using the environment variables `DD_CLIENT_API_KEY`, and `DD_CLIENT_APP_KEY`.

```hcl
credential "datadog" "my_datadog_creds" {
  api_key = env("DD_CLIENT_API_KEY")
  app_key = env("DD_CLIENT_APP_KEY")
}
```
