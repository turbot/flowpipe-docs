---
title:  alicloud
sidebar_label: alicloud
---

# alicloud

The `alicloud` credential can be used to access Alibaba Cloud resources.

```hcl
credential "alicloud" "alicloud" {
   access_key = "LTAI4GBVFakeKey09Kxezv66"
   secret_key = "6iNPvThisIsNotARealSecretk1sZF"
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
