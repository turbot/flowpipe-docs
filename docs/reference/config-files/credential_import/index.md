---
title:  credential_import
sidebar_label: credential_import
---


# Credential Import

> [!IMPORTANT]
> ***As of Tailpipe 1.0.0, `credential_import` has been renamed to `connection_import`. The `credential_import` resource is deprecated and will be removed in a future version of Tailpipe. Use [connection_import](/docs/reference/config-files/connection_import) instead.***


The `credential_import` resource allows you to bulk import credentials from other systems & formats. `credential_import` is a top-level block defined in config files (`*.fpc`) like `credential` and `workspace`.

```hcl
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
  prefix      = "sp1_"
} 
```

Imported credentials are converted to the native Tailpipe credential type - Tailpipe `aws` connections become `aws` credentials, `slack` connections are `slack` credentials, etc.  

The imported credentials are merged into the map of all credentials like any other credential, and they are referenced the same way.  For example, if you have connections defined as:

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
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

Then they will be available in Tailpipe as:

```hcl
credential.gcp.gcp_dev_aaa
credential.gcp.gcp_demo
credential.aws.aws_001
credential.aws.aws_002
credential.slack.slack
```

and they will have the same attributes as the native Tailpipe credential types:

```hcl
credential.gcp.gcp_dev_aaa.access_token
credential.aws.aws_001.env
credential.aws.aws_001.access_key
credential.aws.aws_001.secret_key
credential.aws.aws_001.session_token
credential.slack.slack.token
# etc...
```

If you specify a `prefix`, the credential names will be prepended with it:
```hcl
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
  prefix      = "sp1_" 
} 
```

Results in:

```hcl
credential.gcp.sp1_gcp_dev_aaa
credential.gcp.sp1_gcp_demo
credential.aws.sp1_aws_001
credential.aws.sp1_aws_002
credential.slack.sp1_slack
```

If there is a name conflict for any credentials, Tailpipe will throw an error when loading.

At this time, you may only use a single `import_credential` block.

<!--
 of a given type, though we may revisit this restriction in the future.
-->