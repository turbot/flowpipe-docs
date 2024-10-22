---
title: Run Flowpipe
sidebar_label: Run Flowpipe
---

# Run Flowpipe

Flowpipe is simple to install and manage and does not require any special expertise to get started.  It's distributed as a single binary file - just [download and run it](/downloads)!

## Mod Location
Flowpipe always runs in the context of a [mod](/docs/build), which is a collection of Flowpipe pipelines and triggers.  You can [import and use resources from other mods](/docs/build/mod-dependencies) so you can get started without even writing any code! You can explore the available mods on the [Flowpipe Hub](https://hub.flowpipe.io/).

Flowpipe loads the mod from the current directory by default, but you can pass the [--mod-location](/docs/reference/cli) flag or set the [FLOWPIPE_MOD_LOCATION](/docs/reference/env-vars/flowpipe_mod_location) to set it to a different path.  The event store is also written to this mod location, in the `.flowpipe/store/` subdirectory.

## Configuration Files
Flowpipe will load [configuration files](/docs/reference/config-files) (`*.fpc`) according to the configuration search path.  You can change this path with the `--config-path` argument or the [FLOWPIPE_CONFIG_PATH](/docs/reference/env-vars/flowpipe_config_path) environment variable, but it defaults to `.:$FLOWPIPE_INSTALL_DIR/config` (`.:~/.flowpipe/config`).  This allows you to manage your [workspaces](/docs/run/workspaces) and [connections](/docs/run/connections) centrally in the `~/.flowpipe/config` directory, but override them in the working directory / mod location if desired.


## Operating Modes

Flowpipe can operate in 2 modes.

By default, Flowpipe runs in **Client-only Mode**.  Flowpipe loads the mod, runs the command, and exits. [Triggers](/docs/build/triggers) are not enabled in Client-Only Mode.  [Integrations](/docs/reference/config-files/integration/) are not available in Client-Only mode, and all [message](/docs/flowpipe-hcl/step/message) and [input](/docs/flowpipe-hcl/step/input) steps will route to the console.

If you run Flowpipe in **[Server Mode](/docs/run/server)** mode, Flowpipe will run an API server (on port `7103` by default).  In Server-Mode, you can create [triggers](/docs/flowpipe-hcl/trigger) that run pipelines [on a schedule](/docs/flowpipe-hcl/trigger/schedule) or in response to events such as [webhooks](/docs/flowpipe-hcl/trigger/http).  Your pipelines can send [messages](/docs/flowpipe-hcl/step/message) and prompt for [input](/docs/flowpipe-hcl/step/input) via Slack, Email, or other [integrations](/docs/reference/config-files/integration/). After you start the Flowpipe server, you can run Flowpipe commands against it by specifying the [--host](/docs/reference/cli) argument.
