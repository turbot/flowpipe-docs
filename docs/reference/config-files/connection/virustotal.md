---
title: virustotal
sidebar_label: virustotal
---

# virustotal

The `virustotal` connection can be used to access VirusTotal resources.

```hcl
connection "virustotal" "my_ virustotal" {
  api_key = "SG.R7..."
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API key     |

All arguments are optional, and a `virustotal` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `virustotal` connection type includes an implicit, default connection (`connection.virustotal.default`) that will be configured to set the `api_key` to the `VTCLI_APIKEY` environment variable.

```hcl
connection "virustotal" "default" {
  api_key = env("VTCLI_APIKEY")
}
```
