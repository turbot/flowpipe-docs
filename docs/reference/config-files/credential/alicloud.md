---
title:  alicloud
sidebar_label: alicloud
---

# alicloud

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `alicloud` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [alicloud connection](/docs/reference/config-files/connection/alicloud) instead.***


The `alicloud` credential can be used to access Alibaba Cloud resources.

```hcl
credential "alicloud" "alicloud" {
   access_key = "LTAI4example"
   secret_key = "6iNPXexample
}
```

## Arguments

| Name             | Type    | Required?| Description
|------------------|---------|----------|-------------------
| `access_key`     |  String | Optional | A static access key to use to authenticate.
| `secret_key`     |  String | Optional | A static secret key to use to authenticate.

All arguments are optional, and a `alicloud` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `alicloud` credential type includes an implicit, default credential (`credential.alicloud.default`) that will be configured using either of the following environment variables:

1.`ALIBABACLOUD_ACCESS_KEY_ID` and `ALIBABACLOUD_ACCESS_KEY_SECRET`.

```hcl
credential "alicloud" "default" {
   access_key = env("ALIBABACLOUD_ACCESS_KEY_ID")
   secret_key = env("ALIBABACLOUD_ACCESS_KEY_SECRET")
}
```

2.`ALICLOUD_ACCESS_KEY_ID` and `ALICLOUD_ACCESS_KEY_SECRET`.

```hcl
credential "alicloud" "default" {
   access_key = env("ALICLOUD_ACCESS_KEY_ID")
   secret_key = env("ALICLOUD_ACCESS_KEY_SECRET")
}
```

3.`ALICLOUD_ACCESS_KEY` and `ALICLOUD_SECRET_KEY`.

```hcl
credential "alicloud" "default" {
   access_key = env("ALICLOUD_ACCESS_KEY")
   secret_key = env("ALICLOUD_SECRET_KEY")
}
```
