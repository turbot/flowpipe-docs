---
title:  jira
sidebar_label: jira
---

# jira

The `jira` connection can be used to access Jira resources.

```hcl
connection "jira" "jira_connection" {
   api_token = "ATATT3S........................."
   base_url  = "https://test.atlassian.net/"
   username  = "test@abbb.com"
}
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `api_token`     |  String | Optional | API access token
| `base_url`      |  String | Optional | The Base Url of your Jira API instance
| `username`      |  String | Optional | The user name to access the Jira cloud instance

All arguments are optional, and a `jira` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `jira` connection type includes an implicit, default connection (`connection.jira.default`) that will be configured using the environment variables `JIRA_API_TOKEN` (or `JIRA_TOKEN`), `JIRA_URL`, and `JIRA_USER`.

```hcl
connection "jira" "default" {
   api_token = env("JIRA_API_TOKEN")
   base_url  = env("JIRA_URL")
   username  = env("JIRA_USER")
}
```
