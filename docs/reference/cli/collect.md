---
title: tailpipe collect
sidebar_label: tailpipe collect
---

# tailpipe collect

Run a [collection](/docs/reference/config-files/integration).


## Usage
```bash
tailpipe integration [command] [flags]
```

## Sub-Commands

| Command | Description
|-|-
| `list` | List integrations.
| `show` | Show details for an integration.


## Examples

List integrations:

```bash
tailpipe integration list
```

View integration details:

```bash
tailpipe integration show slack.default
```

List integrations in JSON format:

```bash
tailpipe integration list --output json
```