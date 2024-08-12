---
title: flowpipe trigger
sidebar_label: flowpipe trigger
---

# flowpipe trigger

Manage Flowpipe triggers in the current mod and its direct dependents.

## Usage

```bash
flowpipe trigger [command] [flags]
```

## Sub-Commands

| Command | Description
|-|-
| `list` | List triggers from the current mod.
| `run`  | Run a trigger from the current mod or its direct dependents or from a Flowpipe server instance.
| `show` | List a trigger from the current mod.

## Flags

| Flag | Applies to | Description
|-|-|-
| `--arg string=string` | `run` | Specify the value of a trigger argument. Multiple `--arg` arguments may be passed.
| `--detach`   | `run` | Start the trigger and return immediately.  By default, `flowpipe trigger run` will run the trigger and wait for the results. You may only use `--detach` when running a trigger from a server instance (by specifying `--host`, for example).
| `--var string=string` | `run`| Specify the value of a variable.  Multiple `--var` arguments may be passed. 
| `--var-file strings`| `run`| Specify an .fpvar file containing variable values.
| `--verbose`   | `run` | View detailed event information, including step arguments and attributes.

## Examples

List triggers:

```bash
flowpipe trigger list
```

View trigger details:

```bash
flowpipe trigger show my_trigger
```

List triggers in JSON format:

```bash
flowpipe trigger list --output json
```

Run a trigger in the current mod:

```bash
flowpipe trigger run schedule.my_trigger
```

Run a trigger with verbose output:

```bash
flowpipe trigger run schedule.my_trigger --verbose
```

Run a trigger and pass parameters:

```bash
flowpipe trigger run schedule.my_trigger --arg my_string_param="my name" --arg 'my_list_param=["Owner","Application","Environment"]'
```
