---
title:  opsgenie
sidebar_label: opsgenie
---

# opsgenie

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `opsgenie` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [opsgenie connection](/docs/reference/config-files/connection/opsgenie) instead.***

The `opsgenie` credential can be used to access OpsGenie resources.

```hcl
credential "opsgenie" "my_opsgenie" {
  alert_api_key    = "1234f4f0-3333-4837-90af-0123456789ab"
  incident_api_key = "1234f4f0-1111-4837-90af-9cdef0123456"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `alert_api_key`   |  String | Optional | API key for the alerts API
| `incident_api_key`|  String | Optional | API key for the incidents API

All arguments are optional, and an `opsgenie` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `opsgenie` credential type includes an implicit, default credential (`credential.opsgenie.default`) that will be configured to set the `alert_api_key` to the `OPSGENIE_ALERT_API_KEY` environment variable and `incident_api_key` to `OPSGENIE_INCIDENT_API_KEY`.

```hcl
credential "opsgenie" "default" {
  alert_api_key    = env("OPSGENIE_ALERT_API_KEY")
  incident_api_key = env("OPSGENIE_INCIDENT_API_KEY")
}
```
