---
title: Ask for Input
sidebar_label: Ask for Input
---

# Ask for Input

Flowpipe allows you to optimize and automate your operational and business processes, but there are times when human interaction is required.  For example, a manager may be required to approve a privilege escalation request.  Furthermore, we live in an era of constant communication across multiple channels.  People prefer to use specific tools (Email, Slack, etc.) for communication and expect these interactions to occur in their tools.  The Flowpipe `input` step primitive provides the ability to prompt for human input via multiple channels and wait for a response.

To interact with the various tools and services, Flowpipe also includes an `integration` configuration resource.  This allows you to configure Flowpipe for 2-way communication with your tool (Slack, email, etc).  `integration` is a configuration resource (defined in `.fpc` files) like `connection`, thus the configuration is installation level, not mod level; you can define the integrations for your installation once and refer to them from any mods.

Sending notifications is a common pattern, and often users will want to route a request to more than one user, group, or channel, and via more than one delivery mechanism.  For instance, you may want to request approval via Slack AND email.  The `notifier` resource allows you to define a list of integrations to send notifications to. Like `integration`, a `notifier` is an installation-level configuration resource.

Flowpipe [integrations](/docs/reference/config-files/integration/) require callback endpoints, so they only work in [server-mode](/docs/run/server).  When you run a pipeline in [client-mode](/docs/run#operating-modes), notifications for [input](/docs/flowpipe-hcl/step/input) and [message](/docs/flowpipe-hcl/step/message) steps will only appear on the command line, regardless of your `notifier` and `integration` configuration.  To send [input](/docs/flowpipe-hcl/step/input) and [message](/docs/flowpipe-hcl/step/message) requests to external services, you must run [Flowpipe server](/docs/run/server)!



## Adding an input step to your pipeline

To prompt for input in your pipeline, you can use an [input step](/docs/flowpipe-hcl/step/input).

```hcl
locals {
  games = {
    "Checkers"                 = "https://gametable.org/games/checkers/"
    "Tic Tac Toe"              = "https://gametable.org/games/tic-tac-toe/"
    "Global Thermonuclear War" = "Sorry - the only winning move is not to play."
  }
}

pipeline "play_a_game" {
  param "notifier" {
    default = "default"
  }

  step "input" "choose_game" {
    prompt   = "Shall we play a game?"
    type     = "select"
    notifier = notifier[param.notifier]
    
    option "Tic Tac Toe" {} 
    option "Checkers" {}
    option "Global Thermonuclear War" {} 
  }

  step "message" "play" {
    text     = "Lets Play ${step.input.choose_game.value}! ${local.games[step.input.choose_game.value]}"
    notifier = notifier[param.notifier]
  }
}
```

This pipeline will prompt the user to select a game to play using a `select` box (there are other [input step types](/docs/flowpipe-hcl/step/input#input-types), such as `text`, `button`, and `multiselect`).
After the user selects a game, the pipeline will send back a message with a hyperlink to the selected game.


## Running in Client-Mode

If you run the pipeline in [client-mode](/docs/run#operating-modes), pipes will prompt you on the console and wait for your response. 

![](/build/input_console_select_game.png)


Once you respond, the pipeline will continue.

![](/build/play_game_console.png)


## Running in Server-Mode

Inputs and messages are sent only to the console when running in client-mode, which is suitable for testing your pipelines, or for running pipelines that do not require coordination with others.  Running in server-mode, however, allows you to route messages and inputs to external systems and services, like Slack or email.

When running in server-mode, the `input` and `message` steps route messages to an [integration](/docs/reference/config-files/integration) via a [notifier](/docs/reference/config-files/notifier). You don't need to create these to get started though;  Flowpipe creates a default [`http` integration](/docs/reference/config-files/integration/http) as well as a [default notifier](/docs/reference/config-files/notifier#default-notifier) that routes to it.

Integrations are only loaded in [server mode](/docs/run/server), so let's start the Flowpipe server:
```bash
flowpipe server
```

In another terminal, run the pipeline:
```bash
flowpipe pipeline run input_step_example_01  --host local 
```

Flowpipe runs the pipeline.  When it gets to the input step, it prints the URL for the form and waits:
```bash
$ flowpipe pipeline run play_a_game  --host local
[flowpipe] Execution ID: exec_cnj190jjtoj8h9b7tkag
[flowpipe] Warning: Mod is stale, last loaded 2024-03-04T12:21:10.857114-06:00
[play_a_game] Starting pipeline
[play_a_game.choose_game] Starting input: https://48ca-2600-1702-ee0-16b0-64c3-eb37-9e39-3968.ngrok-free.app/form/cnj190jjtoj8h9b7tkc0/2fup3zqqmb7zl

```

Open the URL in your browser.  The form appears.  

![](/build/input_http_select_game.png)


Select a game and then click the **Submit** button. Return to the terminal where you are running the pipeline.  You will see that the pipeline is now finished and the message appears in the output:

```bash
$ flowpipe pipeline run play_a_game  --host local
[flowpipe] Execution ID: exec_cnj1bv3jtoj8h9b7tkd0
[flowpipe] Warning: Mod is stale, last loaded 2024-03-04T12:21:10.857114-06:00
[play_a_game] Starting pipeline
[play_a_game.choose_game] Starting input: https://48ca-2600-1702-ee0-16b0-64c3-eb37-9e39-3968.ngrok-free.app/form/cnj1bv3jtoj8h9b7tkeg/3oe3jg18n9rf7
[play_a_game.choose_game] Complete 14s
[play_a_game.play] Starting message: Lets Play Global Thermonuclear War!  <https://dosg…
[play_a_game.play] Complete 258ms
[play_a_game] Complete 15s exec_cnj1bv3jtoj8h9b7tkd0
```



## Create an Integration

While the default `http` integration is useful for testing pipelines, you probably want to send input requests to other people via other mechanisms, like [Email](/docs/reference/config-files/integration/email) or [Slack](/docs/reference/config-files/integration/slack).

The `integration` block allows you to define configuration information for communicating with an external system such as Slack or Email.  `integration` blocks are *configuration* resources - they are defined in configuration files (`.fpc`), not mod files(`.fp`).

Set up a [Slack](/docs/reference/config-files/integration/slack#setting-up-as-slack-integration), [Microsoft Teams](/docs/reference/config-files/integration/msteams) or an [Email integration](/docs/reference/config-files/integration/email) to send messages and input requests via Slack, Teams or Email.


## Set up the Notifier

After you add a new `integration`, you can add the integration to your [default notifier](/docs/reference/config-files/notifier) (or create a new one) by adding this to your `~/.flowpipe/config/flowpipe.fpc` file:

```hcl
notifier "default" {
  notify {
    integration = integration.http.default  
  }

  notify {
    integration = integration.slack.default  
  }
}
```


The pipeline that we created earlier already routes the input request to the default notifier.  By adding integrations to the default notifier we can route the input request to Slack and/or email without modifying the pipeline!


![](/build/input_slack_select_game.png)
