---
title: Inputs
sidebar_label: Ask for Input
---

# Prompting for Input

Flowpipe allows you to optimize and automate your operational and business processes, but there are times when human interaction is required.  For example, a manager may be required to approve a privilege escalation request.  Furthermore, we live in an era of constant communication across multiple channels.  People prefer to use specific tools (Email, Slack, etc.) for communication and expect these interactions to occur in their tools.  The Flowpipe `input` step primitive provides the ability to prompt for human input via multiple channels and wait for a response.

To interact with the various tools and services, Flowpipe also includes an `integration` configuration resource.  This allows you to configure Flowpipe for 2-way communication with your tool (Slack, email, etc).  `integration` is a configuration resource (defined in `.fpc` files) like `credential`, thus the configuration is installation level, not mod level; you can define the integrations for your installation once and refer to them from any mods.

Sending notifications is a common pattern, and often users will want to route a request to more than one user, group, or channel, and via more than one delivery mechanism.  For instance, you may want to request approval via Slack AND email.  The `notifier` resource allows you to define a list of integrations to send notifications to. Like `integration`, `notifier` is an installation-level configuration resource.



## Adding an input step to your pipeline

To prompt for input in your pipeline, you can use an [input step](/docs/flowpipe-hcl/step/input).  

```hcl
pipeline "input_step_example_01" {
  param "notifier" {
    default = "default"
  }

  step "input" "approval" {
    title    = "Flowpipe: Approval Request"
    prompt   = "Do you approve?"
    type     = "button"
    notifier = notifier[param.notifier]

    option "Yes" {}
    option "No" {} 
  }

  output "choice" {
    value = step.input.approval.value == "Yes" ? "Approved" : "Denied"
  }
}
```

This input will prompt the user for approval using `Yes` and `No` buttons, but there are other [input step types](/docs/flowpipe-hcl/step/input#input-types), such as `text`, `select`, and `multiselect`.
After the user clicks a button, the pipeline will continue, and we will return the selected button value in the `choice` output.  This is a trivial example.  Usually, you will use the input's `value` in a subsequent step.

The `input` step routes the request to an [integration](/docs/reference/config-files/integration) via a [notifier](/docs/reference/config-files/notifier). You don't need to create these to get started though;  Flowpipe creates a default [webform integration](/docs/reference/config-files/integration/web) as well as a [default notifier](/docs/reference/config-files/notifier#default-notifier) that routes to it. 

Integrations are only loaded in [server mode](/docs/run/server), so let's start the Flowpipe server:
```bash
flowpipe server
```

In another terminal, let's run the pipeline:
```bash
flowpipe pipeline run input_step_example_01  --host local 
```

Flowpipe runs the pipeline.  When it gets to the input step, it prints the URL for the webform and waits:
```bash
[flowpipe] Execution ID: exec_cnb4aobjtojdrgh3gakg
[input_step_example_01] Starting pipeline
[input_step_example_01.approval] Starting input: http://localhost:7103/webform/input/rgh3gam0/3bd5115691383e3f2a95532f3db02f5b8ca02cde802e302b8a9008fad9637220
```

Open the URL in your browser.  The form appears.  

![](/images/build/input_webform_approval.png)


Click one of the buttons, then return to the terminal where you are running the pipeline.  You will see that the pipeline is now finished, and your choice was returned in the output:

```bash
[flowpipe] Execution ID: exec_cnb4aobjtojdrgh3gakg
[input_step_example_01] Starting pipeline
[input_step_example_01.approval] Starting input: http://localhost:7103/webform/input/rgh3gam0/3bd5115691383e3f2a95532f3db02f5b8ca02cde802e302b8a9008fad9637220
[input_step_example_01.approval] Complete 7m14s
[input_step_example_01] Output choice = Approved
[input_step_example_01] Complete 7m14s exec_cnb4aobjtojdrgh3gakg
```



## Creating an Integration

While the default webform integration is useful for testing pipelines, you probably want to send input requests to other people via other mechanisms, like [Email](/docs/reference/config-files/integration/email) or [Slack](/docs/reference/config-files/integration/slack).  

The `integration` block allows you to define configuration information for communicating with an external system such as Slack or Email.  `integration` blocks are *configuration* resources - they are defined in configuration files (`.fpc`), not mod files(`.fp`).  The reason for this is twofold:
- The settings are installation-specific and may contain secrets.
- The settings are usually the same for *all* mods in a given installation.

Let's set up a Slack integration.

First, we'll need to [create an app in Slack](https://api.slack.com/start/quickstart) for Flowpipe.

- Go to the [Slack Apps page](https://api.slack.com/apps/) and click the **Create New App** button.
- In the **Create an app** dialog, choose **From scratch**.
- The **Name app & choose workspace** dialog appears.  Enter a name for your app and select the workspace in which to create it.  Click **Create App**.
- Your app is created and you are sent to the **Basic Information** page for the app.  From the left-hand hand menu, select **OAuth & Permissions**.  In the **Scopes** section, under **Bot Token Scopes**, click **Add an OAuth Scope** and add the `chat:write` scope.
- Scroll back to the top of the page and in the **OAuth Tokens for Your Workspace** section, click the **Install to Workspace** button.  You will be prompted to allow access.  Click **Allow**.
- On the **OAuth & Permissions** page, in the **OAuth Tokens for Your Workspace**, copy the **Bot User OAuth Token**.  This will be the `token` that you use to configure the `integration` in Flowpipe.
- Go back to the **Basic Information** page.  In the **App Credentials** section, copy the **Signing Secret**.   This will be the `signing_secret` that you use to configure the `integration` in Flowpipe.


Now that the app is created, we will create a new Slack integration in Flowpipe.  If you don't already have one, create a `flowpipe.fpc` in your config directory (usually `~/.flowpipe/config/`).  Create a [Slack Integration](/docs/reference/config-files/integration/slack).  Set the `token` and `signing_secret` to the values for your slack app, and set the channel to whichever slack channel you would like to use as the default channel.

```hcl
integration "slack" "default" {
  token           = "xoxb-111111"
  channel         = "#vandelay-industries"
  signing_secret  = "000000000000"
}
```


Because Slack will need to call back to Flowpipe, we will need a publicly available endpoint to call back to.  For this demo, we will use [ngrok](https://ngrok.com/) for this purpose.  Download and install ngrok, and then start it, listening to HTTP on the Flowpipe port:
```bash
ngrok http 7103
```

When ngrok starts, it will create a random public URL to forward to your local system.  Copy this URL (from the `Forwarding` line) - we will use this to set the Flowpipe base URL.  In this case, we are using an ephemeral address from ngrok, so we will just pass the base URL to the `flowpipe server` command.  

You can also set the base URL via the [FLOWPIPE_BASE_URL](/docs/reference/env-vars/flowpipe_base_url) environment variable or the `base_url` [workspace argument](/docs/reference/config-files/workspace), and this may be simpler if you have a stable reverse proxy or public URL.

In another terminal, restart Flowpipe server, passing in the ngrok URL as the base URL:

```bash
flowpipe server --base-url https://0000-1111-2222-ee0-16b0-91dd-fdc0-3333-4444.ngrok-free.app
```

In order to finish setting up the Slack app, we will need to get the Request URL for our Slack integration.  In another terminal, run `flowpipe integration show` to and copy the `Request URL`:

```bash
$ flowpipe integration show slack.default --host local
Name:        slack.default 
Type:        slack
Request URL: https://0000-1111-2222-ee0-16b0-91dd-fdc0-3333-4444.ngrok-free.app/api/latest/hook/integration.slack.default/0000000000000a565c4b466152f660b3bf873b83cd83e27c011c8000000000000
```

Now we need to enable your Slack app for interactivity and complete the Slack setup
- In a browser, go to the [Slack app configuration](https://api.slack.com/apps) page for the app that you created earlier.
- In the settings for the app, go to **Interactivity & Shortcuts**.  Enable **Interactivity**, and set the **Request URL** to the integration's `Request URL`.  Click **Save Changes**.
- Re-install the app in the workspace.  From the menu on the left, select **Install App**, then click **Reinstall to Workspace**.


The Slack app is now configured.  **Note that you must also invite the bot user to any channel that you want to post messages to!**  You can go the the channel in Slack and invite the integration by name: `/invite @Flowpipe Integration`.


## Setup the Notifier

Now that the app is set up and installed in your Slack workspace and the `integration` is set up in Flowpipe, you can add the integration to your [default notifier](/docs/reference/config-files/notifier) (or create a new one) by adding this to your `~/.flowpipe/config/flowpipe.fpc` file:
```hcl
notifier "default" {
  notify {
    integration = integration.webform.default  
  }

  notify {
    integration = integration.slack.default  
  }
}
```

The pipeline that we created earlier already routes the input request to the default notifier, and we have now added our Slack integration to that default notifier.  We can run the pipeline again, and now our input request will route to both the webform and our Slack channel!




