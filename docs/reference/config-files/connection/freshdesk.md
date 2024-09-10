---
title:  freshdesk
sidebar_label: freshdesk
---

# freshdesk

The `freshdesk` connection can be used to access Freshdesk resources.

```hcl
connection "freshdesk" "freshdesk_connection" {
  api_key = "sk-jwgthNa..."
  subdomain = "domain_name"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | Freshdesk API key
| `subdomain`     |  String | Optional | Freshdesk subdomain

All arguments are optional, and a `freshdesk` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `freshdesk` connection type includes an implicit, default connection (`connection.freshdesk.default`) that will be configured using the environment variables `FRESHDESK_API_KEY` and `FRESHDESK_SUBDOMAIN`.

```hcl
connection "freshdesk" "default" {
  api_key = env("FRESHDESK_API_KEY")
  subdomain = env("FRESHDESK_SUBDOMAIN")
}
```
