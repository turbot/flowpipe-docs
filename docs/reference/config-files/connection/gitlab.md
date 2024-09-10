---
title: gitlab
sidebar_label: gitlab
---

# gitlab

The `gitlab` connection can be used to access GitLab resources.

```hcl
connection "gitlab" "my_gitlab_token" {
  token = "glpat-..."
}
```

## Arguments

| Name    | Type   | Required? | Description      |
| ------- | ------ | --------- | ---------------- |
| `token` | String | Optional  | API access token |

All arguments are optional, and a `gitlab` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `gitlab` connection type includes an implicit, default connection (`connection.gitlab.default`) that will be configured to set the `token` to the `GITLAB_TOKEN` environment variable.

```hcl
connection "gitlab" "default" {
  token = env("GITLAB_TOKEN")
}
```
