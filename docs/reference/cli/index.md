---
title: Flowpipe CLI
sidebar_label: Flowpipe CLI
---

# Flowpipe CLI

## Sub-Commands

| Command | Description
|-|-
| [flowpipe help](/docs/reference/cli/help)      | Help about any command
| [flowpipe integration](/docs/reference/cli/integration)   | List and view integrations
| [flowpipe mod](/docs/reference/cli/mod)        | Flowpipe mod management
| [flowpipe notifier](/docs/reference/cli/notifier)   | List and view notifiers
| [flowpipe pipeline](/docs/reference/cli/pipeline) | List, view, and run Flowpipe pipelines
| [flowpipe process](/docs/reference/cli/process) | List and view Flowpipe processes
| [flowpipe server](/docs/reference/cli/server)  | Run Flowpipe server, including triggers and integrations
| [flowpipe trigger](/docs/reference/cli/trigger) | List and view Flowpipe triggers
| [flowpipe variable](/docs/reference/cli/variable) | List and view Flowpipe variables



<!--

| [flowpipe login](/docs/reference/cli/login)    | Log in to Flowpipe CLoud

| [flowpipe completion](/docs/reference/cli/completion)| Generate the autocompletion script for the specified shell

-->
## Global Flags

<table>
<thead>
  <tr> 
    <th> Flag </th> 
    <th> Description </th> 
  </tr>
</thead>

<tbody>
  <tr> 
    <td nowrap="true"> `--config-path` </td> 
    <td>  
    Sets the search path for <a href = "/docs/reference/config-files">configuration files</a>. This argument accepts a colon-separated list of directories.  All configuration files (`*.fpc`) will be loaded from each path, with decreasing precedence.  The default is `.:$FLOWPIPE_INSTALL_DIR/config` (`.:~/.flowpipe/config`).  This allows you to manage your <a href="/docs/reference/config-files/workspace"> workspaces </a> and <a href="/docs/reference/config-files/connection">connections</a> centrally in the `~/.flowpipe/config` directory, but override them in the working directory / mod location if desired.
    </td> 
  </tr>

  <tr> 
    <td nowrap="true"> `--data-dir` </td> 
    <td>  
    Sets the event store data directory. Flowpipe defaults to the `.flowpipe` directory in the current mod directory. This argument allows you to specify a different directory.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `-h`, `--help` </td> 
    <td>  Help for Flowpipe. </td> 
  </tr>
                  
  <tr> 
    <td nowrap="true"> `--host` </td> 
    <td> Run the command against a local or remote server instance.  You may specify the full host and port (e.g. `--host https://flowpipe.my-org.com:7103`), or use the keyword `local` to connect to the local server instance as a shortcut for `https://localhost:7103` (e.g. `--host local`) </td> 
  </tr>

  <tr> 
    <td nowrap="true">  `--input` </td>
    <td> Enable interactive prompts (default `true`). </td>
  </tr>


  <tr> 
    <td nowrap="true">  `--max-concurrency-container int` </td>
    <td>Set the maximum number of `container` step instances that can execute concurrently across all pipeline instances (default `25`). </td>
  </tr>
  <tr> 
    <td nowrap="true">  `--max-concurrency-function int` </td>
    <td> Set the maximum number of `function` step instances that can execute concurrently across all pipeline instances (default `50`). </td>
  </tr>
  <tr> 
    <td nowrap="true">  `--max-concurrency-http int` </td>
    <td> Set the maximum number of `http` step instances that can execute concurrently across all pipeline instances (default `500`). </td>
  </tr>
  <tr> 
    <td nowrap="true">  `--max-concurrency-query int` </td>
    <td> Set the maximum number of `query` step instances that can execute concurrently across all pipeline instances (default `50`). </td>
  </tr>



  <tr> 
    <td nowrap="true"> `--mod-location`  </td> 
    <td> Sets the Flowpipe workspace working directory.  If not specified, the workspace directory will be set to the current working directory.  See <a href="/docs/reference/env-vars/flowpipe_mod_location">FLOWPIPE_MOD_LOCATION</a> for details. </td>
  </tr>

   <tr> 
    <td nowrap="true">  `--output` </td> 
    <td>  Select a console output format: `pretty`, `plain`, `yaml` or `json` (default `pretty`). </td>
  </tr>

  <tr> 
    <td nowrap="true"> `-v`, `--version`  </td> 
    <td>  Display Flowpipe version. </td> 
  </tr>

  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Flowpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/env-vars/flowpipe_workspace">FLOWPIPE_WORKSPACE</a> for details. </td> 
  </tr>
</tbody>

</table>



<!--

  <tr> 
    <td nowrap="true"> `--pipes-host`  </td> 
    <td>  Sets the host used when connecting to Turbot Pipesworkspaces.  See <a href="/docs/reference/env-vars/pipes_host">PIPES_HOST</a> for details. </td>
  </tr>

  <tr> 
    <td nowrap="true"> `--pipes-token`  </td> 
    <td>  Sets the authentication token used when connecting to Turbot Pipes workspaces.  See <a href="/docs/reference/env-vars/pipes_token">PIPES_TOKEN</a> for details. </td>
  </tr>


-->


---


## Exit Codes

|  Value  |   Name                                | Description
|---------|---------------------------------------|----------------------------------------
|   **0** | `ExitCodeSuccessful`                  | Flowpipe ran successfully
|   **1** | `ExitCodeExecutionPaused`             | Flowpipe ran without errors but paused waiting input
|   **2** | `ExitCodeExecutionFailed`             | Flowpipe completed with one or more errors
|   **3** | `ExitCodeExecutionCancelled`          | The Flowpipe command was canceelled by user request
|  **61** | `ExitCodeModInitFailed`               | Mod init failed
|  **62** | `ExitCodeModInstallFailed`            | Mod install failed
| **250** | `ExitCodeInitializationFailed`        | Initialization failed
| **251** | `ExitCodeBindPortUnavailable`         | Network port binding failed
| **252** | `ExitCodeNoModFile`                   | The command requires a mod, but no mod file was found
| **253** | `ExitCodeFileSystemAccessFailure`     | File system access failed
| **254** | `ExitCodeInsufficientOrWrongInputs`   | Runtime error - insufficient or incorrect input
| **255** | `ExitCodeUnknownErrorPanic`           | Runtime error - an unknown panic occurred
