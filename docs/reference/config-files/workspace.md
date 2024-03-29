---
title:  workspace
sidebar_label: workspace
---
# workspace 

A Flowpipe `workspace` is a "profile" that allows you to define options for running Flowpipe.  

```hcl

workspace "my_server" {
  host          = "local"
  listen        = "network"
  port          = 7103
  update_check  = false
  memory_max_mb = 2048
  base_url      = "https://84c5df474.ngrok-free.dev"
}
```

Flowpipe workspaces allow you to define multiple named configurations and easily switch between them using the `--workspace` argument or `FLOWPIPE_WORKSPACE` 
environment variable. 

```bash
flowpipe server --workspace my_server
```

To learn more, see **[Managing Workspaces →](/docs/run/workspaces)**


## Arguments

| Argument            |    Default  | Description
|---------------------|-----------------------------------------------|-----------------------------------------
| `base`              | none                         | A reference to a named workspace resource that this workspace should source its definition from. Any argument can be overridden after sourcing via base.
| `base_url`          | `http://localhost:7103`      | Set the [base URL](/docs/reference/env-vars/flowpipe_base_url) to use for [triggers](/docs/flowpipe-hcl/trigger) and [integrations](/docs/reference/config-files/integration).  This is the base URL that Flowpipe advertises when it interacts with external systems to allow them to call back to Flowpipe.  This may include the DNS or IP address of the system on which you are running Flowpipe, or it may be a reverse proxy such as [ngrok](https://ngrok.com/).
| `host`              | none                         | Set the remote Flowpipe API host to connect to.  This allows you to run Flowpipe commands against a flowpipe host instead of the current working directory.
| `input`             | `true`                       | Enable/Disable interactive prompts for missing variables.  To disable prompts and fail on missing variables, set it to `false`. This is useful when running from scripts.   <br /> <br /> CLI: `--input`
| `listen`            | `network`                    | Specifies the IP addresses on which `flowpipe server` will listen for connections from clients. Currently supported values are `local` (localhost only) or `network` (all IP addresses).
| `log_level`         | off                          | Set the logging output level
| `max_concurrency_container` | `25` | Set the maximum number of `container` step instances that can execute concurrently across all pipeline instances.
| `max_concurrency_function`  | `50` | Set the maximum number of `function` step instances that can execute concurrently across all pipeline instances.
| `max_concurrency_http`      | `500` | Set the maximum number of `http` step instances that can execute concurrently across all pipeline instances.
| `max_concurrency_query`    | `50` | Set the maximum number of `query` step instances that can execute concurrently across all pipeline instances.
| `memory_max_mb`     | `1024`                       | Set a memory soft limit for the flowpipe process. Set to 0 to disable the memory limit. This can also be set via the FLOWPIPE_MEMORY_MAX_MB environment variable.
| `output`            | `pretty`                     | Set the console output format: `pretty`, `plain`, `yaml` or `json`.
| `port`              | `7103`                       | Specifies the TCP port on which `flowpipe server` will listen for connections from clients. 
| `telemetry`         | `info`                       | Set the telemetry level in Flowpipe: `info` or `none` 
| `update_check`      | `true`                       | Enable or disable automatic update checking.
| `watch`             | `true`                       | Watch .mod files for changes when running `flowpipe server`.



Workspaces are defined using the `workspace` block in one or more Flowpipe config files.  Flowpipe will load ALL configuration files (`*.fpc`) from every directory in the [configuration search path](/docs/reference/env-vars/flowpipe_config_path), with decreasing precedence. The set of workspaces is the union of all workspaces defined in these directories.  

The workspace named `default` is special; If a workspace named `default` exists, it will be used whenever the `--workspace` argument is not passed to Flowpipe.  Creating a `default` workspace in `~/.flowpipe/config/workspaces.fpc` provides a way to set all defaults.


Note that the HCL argument names are the same as the equivalent CLI argument names,
except using an underscore in place of a dash:

| Workspace Argument | Environment Variable    | Argument             
|--------------------|-------------------------|----------------------
| `base_url`         | `FLOWPIPE_BASE_URL`     | `--base-url`
| `host`             | `FLOWPIPE_HOST`         | `--host`
| `input`            |                         | `--input` 
| `listen`           | `FLOWPIPE_LISTEN`       | `--listen` 
| `log_level`        | `FLOWPIPE_LOG_LEVEL`    |
| `max_concurrency_container` | `FLOWPIPE_MAX_CONCURRENCY_CONTAINER` |  `--max-concurrency-container`
| `max_concurrency_function` | `FLOWPIPE_MAX_CONCURRENCY_FUNCTION` |  `--max-concurrency-function`
| `max_concurrency_http` | `FLOWPIPE_MAX_CONCURRENCY_HTTP` |  `--max-concurrency-http`
| `max_concurrency_query` | `FLOWPIPE_MAX_CONCURRENCY_QUERY` |  `--max-concurrency-query`
| `memory_max_mb`    | `FLOWPIPE_MEMORY_MAX_MB`|
| `output`           |                         | `--output`
| `port`             | `FLOWPIPE_PORT`         | `--port`
| `telemetry`        | `FLOWPIPE_TELEMETRY`    |
| `update_check`     | `FLOWPIPE_UPDATE_CHECK` | 
| `watch`            |                         | `--watch`



<!--
| `insecure`         | `FLOWPIPE_INSECURE`     | `--insecure` 
| `event_store`      |                         | `--event-store`

| `mod_location`     | `FLOWPIPE_MOD_LOCATION` | `--mod-location`


| `pipes_host`       | `PIPES_HOST`            | `--pipes-host`
| `pipes_token`      | `PIPES_TOKEN`           | `--pipes-token`

| `query_timeout`               | `FLOWPIPE_QUERY_TIMEOUT`      | `--query_timeout`     |
| `workspace_database`          | `FLOWPIPE_WORKSPACE_DATABASE` | `--workspace-database`|


| `search_path`                 | none                           | `--search-path`       |
| `search_path_prefix`          | none                           | `--search-path-prefix`|

| `max_parallel`                | `FLOWPIPE_MAX_PARALLEL`       | `--max-parallel`      |


-->


## Examples


```hcl

workspace "server" {
  host          = "local"
  listen        = "network"
  port          = 7103
  update_check  = false
  memory_max_mb = 2048
  base_url      = "https://84c5df474.ngrok-free.dev"
}

workspace "local_01" {
  output                    = "pretty"
  watch                     = true
  input                     = true
  host                      = "http://localhost:7103"
  port                      = 7103
  listen                    = local
  update_check              = true
  telemetry                 = "info"
  log_level                 = "info"
  memory_max_mb             = 1024
  base_url                  = "https://84c5df474.ngrok-free.dev"
  max_concurrency_container = 50
  max_concurrency_function  = 50
  max_concurrency_http      = 250
  max_concurrency_query     = 50
}

workspace "dev_server" {
  base      = workspace.server
  log_level = "debug"
  port      = 7104
  base_url  = "https://84c5df474.ngrok-free.dev"
}
```