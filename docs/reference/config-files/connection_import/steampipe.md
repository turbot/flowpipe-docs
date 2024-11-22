---
title:  tailpipe
sidebar_label: tailpipe
---


# tailpipe

The `tailpipe` connection import resource allows you to bulk import connections from Tailpipe connections.  This allows you to import the plugin connections from your Tailpipe installation into Tailpipe.

```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `source`        |  String | Optional | Path to the config file(s) with the connections to import.  Defaults to `~/.tailpipe/config/*.spc`.
| `connections`   |  List&ltString&gt | Optional | A list of connection names to import.  You may use the `*` globbing wildcard in these entries.  The default is `["*"]` (all connections).
| `prefix`        | String  | Optional | A string to prepend to the connection name to create the connection name. 

## Examples

### Import ALL Tailpipe connections
```hcl
connection_import "tailpipe" {} 
```
or
```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/*.spc"
  connections = ["*"]
} 
```

### Import ALL Tailpipe connections and prefix them
```hcl
connection_import "tailpipe" {
  prefix = "sp_"
} 
```

### Import specific Tailpipe connections only

```hcl
connection_import "tailpipe" {
  source      = "~/.tailpipe/config/aws.spc"
  connections = ["aws_prod*", "aws_all"]
} 
```