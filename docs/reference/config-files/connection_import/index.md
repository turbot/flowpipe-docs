---
title:  connection_import
sidebar_label: connection_import
---


# Connection Import


The `connection_import` resource allows you to bulk import connections from other systems & formats. `connection_import` is a top-level block defined in config files (`*.tpc`) like `connection` and `workspace`.

```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
  prefix      = "sp1_"
} 
```

Imported connections are converted to the native Tailpipe connection type - Tailpipe `aws` connections become `aws` connections, `slack` connections are `slack` connections, etc.  

The imported connections are merged into the map of all connections like any other connection, and they are referenced the same way.  For example, if you have connections defined as:

```hcl
connection "gcp_dev_aaa" {
  plugin    = "gcp"     
  project   = "dev-aaa"            
}

connection "gcp_demo" {
  plugin    = "gcp"     
  project   = "demo"            
}

connection "aws_001" {
  plugin  = "aws"
  regions = ["*"]
  profile = "aws-001"
}

connection "aws_002" {
  plugin  = "aws"
  regions = ["*"]
  profile = "aws-002"
}

connection "slack" {
  plugin  = "slack"
  token   = "xoxp-12345678902345"
}
```

and import with:

```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

Then they will be available in Tailpipe as:

```hcl
connection.gcp.gcp_dev_aaa
connection.gcp.gcp_demo
connection.aws.aws_001
connection.aws.aws_002
connection.slack.slack
```

and they will have the same attributes as the native Tailpipe connection types:

```hcl
connection.gcp.gcp_dev_aaa.access_token
connection.aws.aws_001.env
connection.aws.aws_001.access_key
connection.aws.aws_001.secret_key
connection.aws.aws_001.session_token
connection.slack.slack.token
# etc...
```

If you specify a `prefix`, the connection names will be prepended with it:
```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
  prefix      = "sp1_" 
} 
```

Results in:

```hcl
connection.gcp.sp1_gcp_dev_aaa
connection.gcp.sp1_gcp_demo
connection.aws.sp1_aws_001
connection.aws.sp1_aws_002
connection.slack.sp1_slack
```

If there is a name conflict for any connections, Tailpipe will throw an error when loading.

At this time, you may only use a single `import_connection` block.

<!--
 of a given type, though we may revisit this restriction in the future.
-->