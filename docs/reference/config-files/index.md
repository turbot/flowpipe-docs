---
title: Configuration Files
sidebar_label: Configuration Files
---

# Configuration Files

Configuration resources like [workspaces](/docs/reference/config-files/workspace) and [connections](/docs/reference/config-files/connection) are defined using HCL in one or more Tailpipe config files.  

Tailpipe will load ALL configuration files (`*.tpc`) from every directory in the [configuration search path](/docs/reference/env-vars/tailpipe_config_path), with decreasing precedence.  By default, the configuration search path includes the current directory (or `--mod-location` if specified) followed by the `config` directory in the [TAILPIPE_INSTALL_DIR](/docs/reference/env-vars/tailpipe_install_dir): `.:$TAILPIPE_INSTALL_DIR/config`.  This allows you to manage your workspaces and connections centrally in the `~/.tailpipe/config` directory, but override them in the working directory / mod location if desired.