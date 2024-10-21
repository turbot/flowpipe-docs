---
title: ipstack
sidebar_label: ipstack
---

# ipstack

The `ipstack` connection can be used to access IpStack resources.

```hcl
connection "ipstack" "my_ipstack" {
  access_key = "1234801fakekeyf123455e6cfaf2"
}
```

## Arguments

| Name         | Type   | Required? | Description    |
| ------------ | ------ | --------- | -------------- |
| `access_key` | String | Optional  | API access key |

All arguments are optional, and a `ipstack` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `ipstack` connection type includes an implicit, default connection (`connection.ipstack.default`) that will be configured to set the `access_key` to the `IPSTACK_ACCESS_KEY` environment variable.

```hcl
connection "ipstack" "default" {
  access_key = env("IPSTACK_ACCESS_KEY")
}
```
