---
title:  steampipe
sidebar_label: steampipe
---


# steampipe

The `steampipe` connection import resource allows you to bulk import connections from Steampipe connections.  This allows you to import the plugin connections from your Steampipe installation into Flowpipe.

```hcl
connection_import "steampipe" {
  source      = "~/.steampipe/config/*.spc"
  connections = ["*"]
} 
```

## Arguments

| Name            | Type    | Required?| Description
|-----------------|---------|----------|-------------------
| `source`        |  String | Optional | Path to the config file(s) with the connections to import.  Defaults to `~/.steampipe/config/*.spc`.
| `connections`   |  List&lt;String&gt; | Optional | A list of connection names to import.  You may use the `*` globbing wildcard in these entries.  The default is `["*"]` (all connections).
| `prefix`        | String  | Optional | A string to prepend to the connection name to create the connection name. 

## Examples

### Import ALL Steampipe connections
```hcl
connection_import "steampipe" {} 
```
or
```hcl
connection_import "steampipe" {
  source      = "~/.steampipe/config/*.spc"
  connections = ["*"]
} 
```

### Import ALL Steampipe connections and prefix them
```hcl
connection_import "steampipe" {
  prefix = "sp_"
} 
```

### Import specific Steampipe connections only

```hcl
connection_import "steampipe" {
  source      = "~/.steampipe/config/aws.spc"
  connections = ["aws_prod*", "aws_all"]
} 
```