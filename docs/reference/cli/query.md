---
title: tailpipe mod
sidebar_label: tailpipe mod
---

# tailpipe mod
Tailpipe mod management.

Mods provide an easy way to share Tailpipe pipelines.  Find mods using the public registry at [hub.tailpipe.io](https://hub.tailpipe.io/).


## Usage
```bash
tailpipe mod [command]
```

## Sub-Commands

| Command | Description
|-|-
| `init`        | Initialize the current directory with a `mod.fp` file 
| `install`     | Install one or more mods and their dependencies
| `list`        | List currently installed mods
| `uninstall`   | Uninstall a mod and its dependencies
| `update`      | Update one or more mods and their dependencies


## Flags

| Flag | Description
|-|-
|` --dry-run` | Show which mods would be installed/updated/uninstalled without modifying them (default `false`).
| `--force` | Install mods even if plugin/cli version requirements are not met (cannot be used with `--dry-run`).
| `--prune` | Remove unused dependencies after installation is complete (default `true`).
| `--pull string` | Specify an [update strategy](#update-strategy): `full`, `latest`, `development`, `minimal` (default `latest`)

## Update Strategy

It is also possible to have more granular control of the update behavior - e.g. when to check for new commits. The -`-pull` argument can be used to specify the update strategy when running `powerpipe update` or `powerpipe install`:

| Strategy | Description
|----------|---------------------------------------------------
| `full`   | Check branches and tags for both latest and accuracy
| `latest` | Update everything to latest, but only branches (not tags) are commit checked
| `development` | Update branches and broken constraints to latest, leave satisfied constraints unchanged
| `minimal`| Only update broken constraints. Do not check branches for new commits

## Git URLs & Private Repos

Tailpipe uses `git` to install and update mods. When you run `tailpipe mod install` or `tailpipe mod update`, Tailpipe will first try using `https` and if that does not work it will try `ssh`.  If your SSH keys are configured properly for `git`, you should be able to pull from private repos that you have access to, as well as public ones.

When publishing mods, you should usually only depend on public mods (hosted in public repos) so that users of your mod don't encounter permissions issues.


## Examples
List installed mods:
```bash
tailpipe mod list
```

Install a mod and add the `require` statement to your `mod.fp`:
```bash
tailpipe mod install github.com/turbot/tailpipe-mod-aws
```

Install an exact version of a mod and update the `require` statement to your `mod.fp`.  This may upgrade or downgrade the mod if it is already installed:
```bash
tailpipe mod install github.com/turbot/tailpipe-mod-aws@0.1.0
```

Install a version of a mod using a semver constraint and update the `require` statement to your `mod.fp`.  This may upgrade or downgrade the mod if it is already installed:
```bash
tailpipe mod install github.com/turbot/tailpipe-mod-aws@'^1'
```

Install a mod from a GitHub tag:
```bash
tailpipe mod install github.com/turbot/tailpipe-mod-aws@my-tag
```

Install a mod from a GitHub branch:
```bash
tailpipe mod install github.com/turbot/tailpipe-mod-aws#main
```

Install a mod from a local directory:
```bash
tailpipe  mod install ../tailpipe-mod-aws
```

Install all mods specified in the `mod.fp` and their dependencies:
```bash
tailpipe mod install
```

Preview what `tailpipe mod install` will do, without actually installing anything:
```bash
tailpipe mod install --dry-run
```

Update a mod to the latest version allowed by its current constraint:
```bash
tailpipe mod update github.com/turbot/tailpipe-mod-aws
```

Update all mods specified in the `mod.fp` and their dependencies to the latest versions that meet their constraints, and install any that are missing:
```bash
tailpipe mod update
```


Uninstall a mod:
```bash
tailpipe mod uninstall github.com/turbot/tailpipe-mod-azure
```

Preview uninstalling a mod, but don't uninstall it:
```bash
tailpipe mod uninstall github.com/turbot/tailpipe-mod-gcp --dry-run
```
