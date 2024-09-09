---
title:  abuseipdb
sidebar_label: abuseipdb
---

# abuseipdb

The `abuseipdb` connection can be used to access AbuseIPDB resources.

```hcl
connection "abuseipdb" "abuseipdb_api_key" {
  api_key = "bfc6f1c42dfakefdxxxx26977977b2xxxsfake98f310123456789de0d"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_key`       |  String | Optional | API Key

All arguments are optional, and an `abuseipdb` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `abuseipdb` connection type includes an implicit, default connection (`connection.abuseipdb.default`) that will be configured to set the `api_key` to the `ABUSEIPDB_API_KEY` environment variable.

```hcl
connection "abuseipdb" "default" {
  api_key = env("ABUSEIPDB_API_KEY")
}
```
