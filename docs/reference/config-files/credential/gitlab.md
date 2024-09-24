---
title:  gitlab
sidebar_label: gitlab
---

# gitlab

> ***As of Flowpipe 0.10.0, `credential` has been renamed to `connection`.  The `gitlab` credential resource is deprecated and will be removed in a future version of Flowpipe. Use the [gitlab connection](/docs/reference/config-files/connection/gitlab) instead.***

The `gitlab` credential can be used to access GitLab resources.

```hcl
credential "gitlab" "my_gitlab_token" {
  token = "glpat-..."
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `token`  |  String | Optional | API access token

All arguments are optional, and a `gitlab` credential with no arguments will behave the same as the [default credential](#default-credential).

## Default Credential

The `gitlab` credential type includes an implicit, default credential (`credential.gitlab.default`) that will be configured to set the `token` to the `GITLAB_TOKEN` environment variable.

```hcl
credential "gitlab" "default" {
  token = env("GITLAB_TOKEN")
}
```
