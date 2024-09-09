---
title:  alicloud
sidebar_label: alicloud
---

# alicloud

The `alicloud` connection can be used to access Alibaba Cloud resources.

```hcl
connection "alicloud" "alicloud" {
   access_key = "LTAI4GBVFakeKey09Kxezv66"
   secret_key = "6iNPvThisIsNotARealSecretk1sZF"
}
```

## Arguments

| Name             | Type    | Required?| Description
|------------------|---------|----------|-------------------
| `access_key`     |  String | Optional | A static access key to use to authenticate.
| `secret_key`     |  String | Optional | A static secret key to use to authenticate.

All arguments are optional, and a `alicloud` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `alicloud` connection type includes an implicit, default connection (`connection.alicloud.default`) that will be configured using either of the following environment variables:

1.`ALIBABACLOUD_ACCESS_KEY_ID` and `ALIBABACLOUD_ACCESS_KEY_SECRET`.

```hcl
connection "alicloud" "default" {
   access_key = env("ALIBABACLOUD_ACCESS_KEY_ID")
   secret_key = env("ALIBABACLOUD_ACCESS_KEY_SECRET")
}
```

2.`ALICLOUD_ACCESS_KEY_ID` and `ALICLOUD_ACCESS_KEY_SECRET`.

```hcl
connection "alicloud" "default" {
   access_key = env("ALICLOUD_ACCESS_KEY_ID")
   secret_key = env("ALICLOUD_ACCESS_KEY_SECRET")
}
```

3.`ALICLOUD_ACCESS_KEY` and `ALICLOUD_SECRET_KEY`.

```hcl
connection "alicloud" "default" {
   access_key = env("ALICLOUD_ACCESS_KEY")
   secret_key = env("ALICLOUD_SECRET_KEY")
}
```
