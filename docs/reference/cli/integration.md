---
title: flowpipe integration
sidebar_label: flowpipe integration
---

# flowpipe integration

List and show Flowpipe [integrations](/docs/reference/config-files/integration).


## Usage
```bash
flowpipe integration [command] [flags]
```

## Sub-Commands

| Command | Description
|-|-
| `list` | List integrations.
| `show` | Show details for an integration.


## Examples

List integrations:

```bash
flowpipe integration list
```

View integration details:

```bash
flowpipe integration show slack.default
```

List integrations in JSON format:

```bash
flowpipe integration list --output json
```