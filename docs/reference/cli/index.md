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
  <tr> 
    <th> Flag </th> 
    <th> Description </th> 
  </tr>

  <tr> 
    <td nowrap="true"> <inlineCode>--config-path</inlineCode> </td> 
    <td>  
    Sets the search path for <a href = "/docs/reference/config-files">configuration files</a>. This argument accepts a colon-separated list of directories.  All configuration files (<inlineCode>*.fpc</inlineCode>) will be loaded from each path, with decreasing precedence.  The default is <inlineCode>.:$FLOWPIPE_INSTALL_DIR/config</inlineCode> (<inlineCode>.:~/.flowpipe/config</inlineCode>).  This allows you to manage your <a href="/docs/reference/config-files/workspace"> workspaces </a> and <a href="/docs/reference/config-files/credential">credentials</a> centrally in the <inlineCode>~/.flowpipe/config</inlineCode> directory, but override them in the working directory / mod location if desired.
    </td> 
  </tr>

  <tr> 
    <td nowrap="true"> <inlineCode>--event-store</inlineCode> </td> 
    <td>  
    Sets the event store database file. Flowpipe defaults to the `.flowpipe/flowpipe.db` file in the current mod directory. This argument allows you to specify a different file.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> <inlineCode>-h</inlineCode>, <inlineCode>--help</inlineCode> </td> 
    <td>  Help for Flowpipe. </td> 
  </tr>
                  
  <tr> 
    <td nowrap="true"> <inlineCode>--host</inlineCode> </td> 
    <td> Run the command against a local or remote server instance.  You may specify the full host and port (e.g. <inlineCode>--host https://flowpipe.my-org.com:7103</inlineCode>), or use the keyword <inlineCode>local</inlineCode> to connect to the local server instance as a shortcut for <inlineCode>https://localhost:7103</inlineCode> (e.g. <inlineCode>--host local</inlineCode>) </td> 
  </tr>

  <tr> 
    <td nowrap="true">  <inlineCode>--input</inlineCode> </td>
    <td> Enable interactive prompts (default <inlineCode>true</inlineCode>). </td>
  </tr>


  <tr> 
    <td nowrap="true">  <inlineCode>--max-concurrency-container int</inlineCode> </td>
    <td>Set the maximum number of <inlineCode>container</inlineCode> step instances that can execute concurrently across all pipeline instances (default <inlineCode>25</inlineCode>). </td>
  </tr>
  <tr> 
    <td nowrap="true">  <inlineCode>--max-concurrency-function int</inlineCode> </td>
    <td> Set the maximum number of <inlineCode>function</inlineCode> step instances that can execute concurrently across all pipeline instances (default <inlineCode>50</inlineCode>). </td>
  </tr>
  <tr> 
    <td nowrap="true">  <inlineCode>--max-concurrency-http int</inlineCode> </td>
    <td> Set the maximum number of <inlineCode>http</inlineCode> step instances that can execute concurrently across all pipeline instances (default <inlineCode>500</inlineCode>). </td>
  </tr>
  <tr> 
    <td nowrap="true">  <inlineCode>--max-concurrency-query int</inlineCode> </td>
    <td> Set the maximum number of <inlineCode>query</inlineCode> step instances that can execute concurrently across all pipeline instances (default <inlineCode>50</inlineCode>). </td>
  </tr>



  <tr> 
    <td nowrap="true"> <inlineCode>--mod-location</inlineCode>  </td> 
    <td> Sets the Flowpipe workspace working directory.  If not specified, the workspace directory will be set to the current working directory.  See <a href="/docs/reference/env-vars/flowpipe_mod_location">FLOWPIPE_MOD_LOCATION</a> for details. </td>
  </tr>

   <tr> 
    <td nowrap="true">  <inlineCode>--output</inlineCode> </td> 
    <td>  Select a console output format: <inlineCode>pretty</inlineCode>, <inlineCode>plain</inlineCode>, <inlineCode>yaml</inlineCode> or <inlineCode>json</inlineCode> (default <inlineCode>pretty</inlineCode>). </td>
  </tr>

  <tr> 
    <td nowrap="true"> <inlineCode>-v</inlineCode>, <inlineCode>--version</inlineCode>  </td> 
    <td>  Display Flowpipe version. </td> 
  </tr>

  <tr> 
    <td nowrap="true"> <inlineCode>--workspace	</inlineCode>  </td> 
    <td>  Sets the Flowpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/env-vars/flowpipe_workspace">FLOWPIPE_WORKSPACE</a> for details. </td> 
  </tr>

</table>



<!--

  <tr> 
    <td nowrap="true"> <inlineCode>--pipes-host</inlineCode>  </td> 
    <td>  Sets the host used when connecting to Turbot Pipesworkspaces.  See <a href="/docs/reference/env-vars/pipes_host">PIPES_HOST</a> for details. </td>
  </tr>

  <tr> 
    <td nowrap="true"> <inlineCode>--pipes-token</inlineCode>  </td> 
    <td>  Sets the authentication token used when connecting to Turbot Pipes workspaces.  See <a href="/docs/reference/env-vars/pipes_token">PIPES_TOKEN</a> for details. </td>
  </tr>


-->


---

