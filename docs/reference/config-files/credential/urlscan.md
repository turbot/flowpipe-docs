---
title:  urlscan
sidebar_label: urlscan
---

# urlscan

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `urlscan` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [urlscan connection](/docs/reference/config-files/connection/urlscan) instead.***


The `urlscan` credential can be used to access URLScan resources.

```hcl
credential "urlscan" "my_urlscan" {
  api_key = "11111111-e127-1234-adcd-59cad2f12abc"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API Key

All arguments are optional, and a `urlscan` credential with no arguments will behave the same as the [default credential](#default-credential).

## Attributes (Read-Only)

| Attribute       | Type    | Description
|-----------------|---------|-----------------
| `env`           | Map     | A map of the resolved credential-related environment variables (`URLSCAN_API_KEY`)

## Default Credential

The `urlscan` credential type includes an implicit, default credential (`credential.urlscan.default`) that will be configured to set the `api_key` to the `URLSCAN_API_KEY` environment variable.

```hcl
credential "urlscan" "default" {
  api_key = env("URLSCAN_API_KEY")
}
```
