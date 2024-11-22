---
title: Managing Connections
sidebar_label: Connections
---

# Managing Connections

Tailpipe **Connections** provide a mechanism for defining credentials and options for interacting with external systems.

**Connections** provide *credentials* and *configuration options* to connect to external services.  Tailpipe connections are similar to connections in Steampipe and Flowpipe.

```hcl
connection "aws" "aws_01" {
  profile = "aws_01"
}

connection "aws" "aws_03" {
  access_key = "AKIA4YFAKEKEYXTDS252"
  secret_key = "SH42YMW5p3EThisIsNotRealzTiEUwXN8BOIOF5J8m"
}

connection "gcp" "gcp_my_other_project" {
  project     = "my-other-project"
  credentials = "/home/me/my-service-account-creds.json"
}
```

For Steampipe users, there is also a *connection import*.

```hcl
connection_import "steampipe" {
  source      = "~/.steampipe/config/*.spc"
  connections = ["*"]
  prefix      = "sp1_"
}
```
