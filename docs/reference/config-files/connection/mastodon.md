---
title: mastodon
sidebar_label: mastodon
---

# mastodon

The `mastodon` connection can be used to access Mastodon resources.

```hcl
connection "mastodon" "mastodon_connection" {
  server = "https://myserver.social"
  access_token = "FK1_fake-token"
}
```

## Arguments

| Name           | Type   | Required? | Description                             |
| -------------- | ------ | --------- | --------------------------------------- |
| `server`       | String | Optional  | The federated server your account lives |
| `access_token` | String | Optional  | Mastodon access token                   |

All arguments are optional, and a `mastodon` connection with no arguments will behave the same as the [default connection](#default-connection).
