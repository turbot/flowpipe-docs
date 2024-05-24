---
title: flowpipe variable
sidebar_label: flowpipe variable
---

# flowpipe variable

Manage Flowpipe variables in the current mod and its direct dependents.

## Usage
```bash
flowpipe variable [command]
flowpipe variable show variable_name [args]
```


## Sub-Commands

| Command | Description
|-|-
| `list` | List variables from the current mod and its direct dependents.
| `show` | Show details of a variable from the current mod or its direct dependents.


## Examples


List variables:
```bash
flowpipe variable list
```


List all variables in `JSON` format:
```bash
flowpipe variable list --output json
```

List variables using settings from a workspace:
```bash
flowpipe variable list --workspace my_workspace
```

Show details of a single variable in the current mod:
```bash
flowpipe variable show mandatory_tags
```


Show details of a single variable in a direct dependency mod:
```bash
flowpipe variable show aws_tags.mandatory_tags
```

Show details of a variable in `JSON` format:
```bash
flowpipe variable show mandatory_tags --output json
```

Show details of a variable using settings from a workspace:
```bash
flowpipe variable show mandatory_tags -workspace my_workspace
```
