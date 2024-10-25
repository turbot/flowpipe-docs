---
title:  virustotal
sidebar_label: virustotal
---

# virustotal

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `virustotal` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [virustotal connection](/docs/reference/config-files/connection/virustotal) instead.***

The `virustotal` credential can be used to access VirusTotal resources.

```hcl
credential "virustotal" "my_ virustotal" {
  api_key = "SG.R7..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`         |  String | Optional | API key

All arguments are optional, and a `virustotal` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `virustotal` credential type includes an implicit, default credential (`credential.virustotal.default`) that will be configured to set the `api_key` to the `VTCLI_APIKEY` environment variable.

```hcl
credential "virustotal" "default" {
  api_key = env("VTCLI_APIKEY")
}
```
