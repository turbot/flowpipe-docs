---
title:  abuseipdb
sidebar_label: abuseipdb
---

# abuseipdb

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `abuseipdb` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [abuseipdb connection](/docs/reference/config-files/connection/abuseipdb) instead.***


The `abuseipdb` credential can be used to access AbuseIPDB resources.

```hcl
credential "abuseipdb" "abuseipdb_api_key" {
  api_key = "bfc6f1c42dfakefdxxxx26977977b2xxxsfake98f310123456789de0d"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API Key

All arguments are optional, and an `abuseipdb` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `abuseipdb` credential type includes an implicit, default credential (`credential.abuseipdb.default`) that will be configured to set the `api_key` to the `ABUSEIPDB_API_KEY` environment variable.

```hcl
credential "abuseipdb" "default" {
  api_key = env("ABUSEIPDB_API_KEY")
}
```
