---
title:  tailpipe
sidebar_label: tailpipe
---


# tailpipe

> [!IMPORTANT]
> ***As of Tailpipe 1.0.0, `credential_import` has been renamed to `connection_import`. The `credential_import` resource is deprecated and will be removed in a future version of Tailpipe. Use [connection_import](/docs/reference/config-files/connection_import) instead.***


The `tailpipe` credential import resource allows you to bulk import credentials from Tailpipe connections.  This allows you to import the plugin credentials from your Tailpipe installation into Tailpipe.

```hcl
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `source`        |  String | Optional | Path to the config file(s) with the connections to import.  Defaults to `~/.tailpipe/config/*.spc`.
| `connections`   |  List&ltString&gt | Optional | A list of connection names to import.  You may use the `*` globbing wildcard in these entries.  The default is `["*"]` (all connections).
| `prefix`        | String  | Optional | A string to prepend to the connection name to create the credential name. 

## Examples

### Import ALL Tailpipe connections
```hcl
credential_import "tailpipe" {} 
```
or
```hcl
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

### Import ALL Tailpipe connections and prefix them
```hcl
credential_import "tailpipe" {
  prefix = "sp_"
} 
```

### Import specific Tailpipe connections only

```hcl
credential_import "tailpipe" {
  source      = "~/.tailpipe/config/aws.spc"
  connections = ["aws_prod*", "aws_all"]
} 
```