---
title:  mastodon
sidebar_label: mastodon
---

# mastodon

> ***As of Flowpipe 1.0.0, `credential` has been renamed to `connection`.  The `mastodon` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [mastodon connection](/docs/reference/config-files/connection/mastodon) instead.***

The `mastodon` credential can be used to access Mastodon resources.

```hcl
credential "mastodon" "mastodon_creds" {
  server = "https://myserver.social"
  access_token = "FK1_fake-token"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|--------------------------------------
| `server`        |  String | Optional | The federated server your account lives
| `access_token`  |  String | Optional | Mastodon access token

All arguments are optional, and a `mastodon` credential with no arguments will behave the same as the [default credential](#default-credential).
