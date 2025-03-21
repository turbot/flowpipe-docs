---
title:  steampipe
sidebar_label: steampipe
---


# steampipe

> [!IMPORTANT]
> ***As of Flowpipe 1.0.0, `credential_import` has been renamed to `connection_import`. The `credential_import` resource is deprecated and will be removed in a future version of Flowpipe. Use [connection_import](/docs/reference/config-files/connection_import) instead.***


The `steampipe` credential import resource allows you to bulk import credentials from Steampipe connections.  This allows you to import the plugin credentials from your Steampipe installation into Flowpipe.

```hcl
credential_import "steampipe" {
  source      = "~/.steampipe/config/*.spc"
  connections = ["*"]
} 
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `source`        |  String | Optional | Path to the config file(s) with the connections to import.  Defaults to `~/.steampipe/config/*.spc`.
| `connections`   |  List&lt;String&gt; | Optional | A list of connection names to import.  You may use the `*` globbing wildcard in these entries.  The default is `["*"]` (all connections).
| `prefix`        | String  | Optional | A string to prepend to the connection name to create the credential name. 

## Examples

### Import ALL Steampipe connections
```hcl
credential_import "steampipe" {} 
```
or
```hcl
credential_import "steampipe" {
  source      = "~/.steampipe/config/*.spc"
  connections = ["*"]
} 
```

### Import ALL Steampipe connections and prefix them
```hcl
credential_import "steampipe" {
  prefix = "sp_"
} 
```

### Import specific Steampipe connections only

```hcl
credential_import "steampipe" {
  source      = "~/.steampipe/config/aws.spc"
  connections = ["aws_prod*", "aws_all"]
} 
```