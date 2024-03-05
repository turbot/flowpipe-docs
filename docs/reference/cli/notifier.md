---
title: flowpipe notifier
sidebar_label: flowpipe notifier
---

# flowpipe notifier

List and show Flowpipe [notifiers](/docs/reference/config-files/notifier).


## Usage
```bash
flowpipe notifier [command] [flags]
```

## Sub-Commands

| Command | Description
|-|-
| `list` | List notifiers.
| `show` | Show details for a notifier.


## Examples

List notifiers:

```bash
flowpipe notifier list
```

View notifier details:

```bash
flowpipe notifier show default
```

List notifiers in JSON format:

```bash
flowpipe notifier list --output json
```