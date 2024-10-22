---
title:  freshdesk
sidebar_label: freshdesk
---

# freshdesk

> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `freshdesk` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [freshdesk connection](/docs/reference/config-files/connection/freshdesk) instead.***

The `freshdesk` credential can be used to access Freshdesk resources.

```hcl
credential "freshdesk" "freshdesk_creds" {
  api_key = "sk-jwgthNa..."
  subdomain = "domain_name"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | Freshdesk API key
| `subdomain`     |  String | Optional | Freshdesk subdomain

All arguments are optional, and a `freshdesk` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `freshdesk` credential type includes an implicit, default credential (`credential.freshdesk.default`) that will be configured using the environment variables `FRESHDESK_API_KEY` and `FRESHDESK_SUBDOMAIN`.

```hcl
credential "freshdesk" "default" {
  api_key = env("FRESHDESK_API_KEY")
  subdomain = env("FRESHDESK_SUBDOMAIN")
}
```
