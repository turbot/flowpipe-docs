---
title:  Environment Variables
sidebar_label:  Environment Variables
---

# Environment Variables

Tailpipe supports environment variables to allow you to change its default behavior.  These are optional settings - You are not required to set any environment variables.

Note that plugins may also support environment variables, but these are plugin-specific - refer to your plugin's documentation on the [Tailpipe Hub](https://hub.tailpipe.io/) for details.

## Tailpipe Environment Variables

| Command | Default | Description
|-|-|-
| [TAILPIPE_CONFIG_PATH](/docs/reference/env-vars/tailpipe_config_path)  | `.:$TAILPIPE_INSTALL_DIR/config` | Set the search path for [configuration files](/docs/reference/config-files).
| [TAILPIPE_LOG_LEVEL](reference/env-vars/tailpipe_log)  | `warn` | Set the logging output level.