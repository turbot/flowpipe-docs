---
title: servicenow
sidebar_label: servicenow
---

# servicenow

The `servicenow` connection can be used to access ServiceNow resources.

```hcl
connection "servicenow" "snow_connection" {
  instance_url = "https://my-instance.service-now.com"
  username = "john.hill"
  password = "fake-p@ss"
}
```

## Arguments

| Name           | Type   | Required? | Description                  |
| -------------- | ------ | --------- | ---------------------------- |
| `instance_url` | String | Optional  | Your ServiceNow instance URL |
| `username`     | String | Optional  | ServiceNow username          |
| `password`     | String | Optional  | ServiceNow app password      |

All arguments are optional, and a `servicenow` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `servicenow` connection type includes an implicit, default connection (`connection.servicenow.default`) that will be configured using the environment variables `SERVICENOW_INSTANCE_URL`, `SERVICENOW_USERNAME`, and `SERVICENOW_PASSWORD`.

```hcl
connection "servicenow" "default" {
  instance_url = env("SERVICENOW_INSTANCE_URL")
  username = env("SERVICENOW_USERNAME")
  password = env("SERVICENOW_PASSWORD")
}
```
