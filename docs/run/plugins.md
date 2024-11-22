---
title: Managing Plugins
sidebar_label: Plugins
---

Tailpipe provides an integrated, standardized SQL interface for querying various sources of log data. It relies on plugins to define and implement tables for those logs. This approach decouples the Tailpipe core from provider-specific implementations, providing flexibility and extensibility.

# Managing Plugins

Tailpipe plugins are packaged as Open Container Images (OCI) and stored in the [Tailpipe Hub registry](https://hub.tailpipe.io).  This registry contains a curated set of plugins developed by and/or vetted by Turbot.  To install the latest version of a standard plugin, you can simply install it by name.

For example, to install the latest `aws` plugin:
```
$ tailpipe plugin install aws
```

This will download the latest aws plugin from the hub registry, and will set up a default connection named `aws`.

> Note: If you install multiple versions of a plugin only the first installation will create a connection automatically for you, you will need to create/edit a [connection](/docs/managing/connections) configuration file in order to use the additional versions of the plugin. 

### Installing a Specific Version
To install a specific version, simply specify the version tag after the plugin name, separated by `@` or `:`

For example, to install the 0.118.0 version of the aws plugin:
```
$ tailpipe plugin install aws@0.118.0
```

This will download the aws plugin version 0.118.0 (the one with the `0.118.0` tag) from the hub registry. 

## Installing from a SemVer Constraint

Plugins should follow [semantic versioning](https://semver.org/) guidelines, and they are tagged in the registry with a **version tag** that specifies their *exact version* in the `major.minor.patch` format (e.g. `1.0.1`).

The intent of the version tag is that it is immutable - while it is technically possible to move the version tag to a different image version, this should not be done.

Installing with a semver constraint allows you to "lock" (or pin) to a specific set of releases which match the contraints. 

If you install via `tailpipe plugin install aws@^1`, for example, `tailpipe plugin update` (and auto-updates) will only update to versions greater than `1.0.0` but less than `2.0.0`.

Supported semver constraint types:

**Wildcard Constraint**: This matches any version for a particular segment (Major, Minor, or Patch).
- `1.x.x` would match any version with major segment of `1`.
- `1.2.x` would match any version with the major segment of `1` and a minor segment of `2`.

**Caret Constraint (^)**: This matches versions that do not modify the left-most non-zero digit.
- `^1.2.3` is the latest version equal or greater than `1.2.3`, but less than `2.0.0`.
- `^0.1.2` is the latest version equal or greater than `0.1.2`, but less than `0.2.0`.

**Tilde Constraint (~)**: This matches versions based on expression, if minor segment is expressed, locks to it, else locks to major.
- `~1` is the latest version greater than or equal to `1.0.0`, but less than `2.0.0` (same as `1.x.x`).
- `~1.2` is the latest version greater than or equal to `1.2.0`, but less than `1.3.0` (same as `1.2.x`).
- `~1.2.3` is the latest version greater than or equal to `1.2.3`, but less than `1.3.0`.

**Range Constraint**: This specifies a range of versions using a hyphen.
- `1.2.3-1.2.5` would limit to latest available version of `1.2.3`,`1.2.4` or `1.2.5`.

**Other Constraints**:
- `>1.1.1` would match any version greater than `1.1.1`.
- `>=1.2.0` would match any version greater than or equal to `1.2.0`.

You can use the install command in the same way as a specific version with these constraints (`imagename@constraint`) syntax:

> Note: For some constraints using special characters `>`, `<`, `*` you may need to escape the characters `\>` or quote the string `tailpipe plugin install "aws@>0.118.0"` depending on your terminal.

- To install the latest version locked to a specific major version:
```bash
$ tailpipe plugin install aws@^2
# or
$ tailpipe plugin install aws@2.x.x
```

- To install the latest version locked to a specific minor version:
```bash 
$ tailpipe plugin install aws@~2.1
# or
$ tailpipe plugin install aws@2.1.x
```

## Installing from another registry

Tailpipe plugins are packaged in OCI format and can be hosted and installed from any artifact repository or container registry that supports OCI V2 images. To install a plugin from a repository, specify the full path in the install command:

```
$ tailpipe plugin install us-docker.pkg.dev/myproject/myrepo/myplugin@mytag
```

## Installing from a File

A plugin binary can be installed manually, and this is often convenient when developing the plugin. Tailpipe will attempt to load any plugin that is referred to in a `connection` configuration:
- The plugin binary file must have a `.plugin` extension
- The plugin binary must reside in a subdirectory of the `~/.tailpipe/plugins/` directory and must be the ONLY `.plugin` file in that subdirectory
- The `connection` must specify the path (relative to `~/.tailpipe/plugins/`) to the plugin in the `plugin` argument

For example, consider a `myplugin` plugin that you have developed.  To install it:
- Create a subdirectory `.tailpipe/plugins/local/myplugin`
- Name your plugin binary `myplugin.plugin`, and copy it to `.tailpipe/plugins/local/myplugin/myplugin.plugin` 
- Create a `~/.tailpipe/config/myplugin.spc` config file containing a connection definition that points to your plugin:
    ```hcl
    connection "myplugin" {
        plugin    = "local/myplugin"                 
    }
    ```

## Installing Missing Plugins

You can install all missing plugins that are referenced in your configuration files:

```bash
$ tailpipe plugin install
```

Running `tailpipe plugin install` with no arguments will cause Tailpipe to read all `connection` and `plugin` blocks in all `.spc` files in the `~/.tailpipe/config` directory and install any that are referenced but are not installed.  Note that when doing so, any default `.spc` file that does not exist in the configuration will also be copied.  You may pass the `--skip-config` flag if you don't want to copy these files:

```bash
$ tailpipe plugin install --skip-config
```


## Viewing Installed Plugins
You can list the installed plugins with the `tailpipe plugin list` command:

```
$ tailpipe plugin list
┌─────────────────────────────────────────────────────┬─────────┬─────────────────────────────────────────────┐
│ NAME                                                │ VERSION │ CONNECTIONS                                 │
├─────────────────────────────────────────────────────┼─────────┼─────────────────────────────────────────────┤
│ hub.tailpipe.io/plugins/turbot/aws@latest          │ 0.4.0   │ aws,aws_account_aaa,aws_account_aab         │
│ hub.tailpipe.io/plugins/turbot/digitalocean@latest │ 0.1.0   │ digitalocean                                │
│ hub.tailpipe.io/plugins/turbot/gcp@latest          │ 0.0.6   │ gcp_project_a,gcp,gcp_project_b             │
│ hub.tailpipe.io/plugins/turbot/github@latest       │ 0.0.5   │ github                                      │
│ hub.tailpipe.io/plugins/turbot/tailpipe@latest    │ 0.0.2   │ tailpipe                                   │
└─────────────────────────────────────────────────────┴─────────┴─────────────────────────────────────────────┘
```

## Updating Plugins

To update a plugin to the latest version for a given stream, you can use the  `tailpipe plugin update` command:

```
tailpipe plugin update plugin_name[@stream]
``` 

The syntax and semantics are identical to the install command -  `tailpipe plugin update aws` will get the latest aws plugin, `tailpipe plugin update aws@1` will get the latest in the 1.x major stream, etc.


To update **all** plugins to the latest in the installed stream:
```bash
tailpipe plugin update --all
```


## Removing Plugins
You can uninstall a plugin with the `tailpipe plugin remove` command:

```
tailpipe plugin uninstall [plugin]
```  

Note that you cannot remove a plugin if there are active connections using it.  You must remove any connections that use the plugin first:

```
$ tailpipe plugin list
+--------------------------------------------------+---------+-------------+
|                       NAME                       | VERSION | CONNECTIONS |
+--------------------------------------------------+---------+-------------+
| hub.tailpipe.io/plugins/turbot/aws@latest       | 0.0.5   | aws         |
| hub.tailpipe.io/plugins/turbot/tailpipe@latest | 0.0.1   | tailpipe   |
+--------------------------------------------------+---------+-------------+

$ tailpipe plugin remove tailpipe
Error: Failed to remove plugin 'tailpipe' - there are active connections using it: 'tailpipe'

$ rm ~/.tailpipe/config/tailpipe.spc 

$ tailpipe plugin remove tailpipe
Removed plugin tailpipe
$ tailpipe plugin list
+--------------------------------------------+---------+-------------+
|                    NAME                    | VERSION | CONNECTIONS |
+--------------------------------------------+---------+-------------+
| hub.tailpipe.io/plugins/turbot/aws@latest | 0.0.5   | aws         |
+--------------------------------------------+---------+-------------+
```

## Tailpipe Plugin Registry Support Lifecycle

The Tailpipe Plugin Registry is committed to ensuring accessibility and stability for its users by maintaining versions of plugins for at least one year and preserving at least one version of each plugin. This practice ensures that users can access older versions of plugins if needed, providing a safety net for compatibility issues or preferences.