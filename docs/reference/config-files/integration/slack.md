---
title:  Slack Integration
sidebar_label: slack
---

# Slack Integration

Use the `slack` integration to send Slack messages with a [`message` step](/docs/flowpipe-hcl/step/message) or to prompt for user input in an [`input` step](/docs/flowpipe-hcl/step/input).


```hcl
integration "slack" "my_slack_app" {
  token           = "xoxp-111111"
  channel         = "#infosec"
  signing_secret  = "Q#$$#@#$$#W"
}
```

## Arguments


| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `token`         | String	  | Optional	  | The Slack user token for the app.  The token must have the required scopes. You must specify either a `token` or `webhook_url`.
| `webhook_url `  | String	  | Optional	  | A Slack webhook url to post to a slack channel. Note that a webhook is tied to a specific channel; if you specify a channel in the step options it will be ignored. You must specify either a `token` or `webhook_url`.
| `channel`       | String	  | Optional	  | The Slack channel to send to.  This value should be considered a default; if an `input` step specifies a `channel` it will override this value.   Note that a webhook is tied to a specific channel, so if you specify a `webhook_url` then this setting will have no effect.
| `signing_secret`| String    | Optional    | Slack signing secret used to verify that the responses are from Slack.  This setting only applies if you use a `token`.
| `title`         | String    | Optional    | Display title for the integration.


## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `request_url`   | String  | The webhook URL to configure as the request url in your Slack app


## Examples

Slack integration requires exactly one of:
- A slack **token**
- A **webhook_url**.  Note that a webhook is tied to a specific channel; if you specify a channel in the step options it will be ignored.


### Example: Slack with token

```hcl
integration "slack" "my_slack_app" {
  token           = "xoxp-111111"
  channel         = "#infosec"
  signing_secret  = "Q#$$#@#$$#W"
}
```


### Example: Slack with webhook URL

```hcl
integration "slack" "my_slack_app" {
  webhook_url = "https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX"
}
```


## Setting up as Slack Integration

To prompt for input via Slack you need to:
1. [Create an app in Slack](#create-the-slack-app).
1. [Create a Slack `integration`](#create-the-flowpipe-integration).
1. [Enable Interactivity in the Slack App](#enable-interactivity-in-the-slack-app).
1. [Add the integration to a `notifier`](#setup-the-notifier).
1. Use an [input step](/docs/flowpipe-hcl/step/input) or a [message step](/docs/flowpipe-hcl/step/message) to send to the notifier.

### Create the Slack App
First, we'll need to [create an app in Slack](https://api.slack.com/start/quickstart) for Flowpipe.

1. Go to the [Slack Apps page](https://api.slack.com/apps/) and click the **Create New App** button.
1. In the **Create an app** dialog, choose **From scratch**.
1. The **Name app & choose workspace** dialog appears.  Enter a name for your app and select the workspace in which to create it.  Click **Create App**.
1. Your app is created and you are sent to the **Basic Information** page for the app.  From the left-hand hand menu, select **OAuth & Permissions**.  In the **Scopes** section, under **Bot Token Scopes**, click **Add an OAuth Scope** and add the `chat:write` scope.
1. Scroll back to the top of the page and in the **OAuth Tokens for Your Workspace** section, click the **Install to Workspace** button.  You will be prompted to allow access.  Click **Allow**.
1. On the **OAuth & Permissions** page, in the **OAuth Tokens for Your Workspace**, copy the **Bot User OAuth Token**.  This will be the `token` that you use to configure the `integration` in Flowpipe.
1. Go back to the **Basic Information** page.  In the **App Credentials** section, copy the **Signing Secret**.   This will be the `signing_secret` that you use to configure the `integration` in Flowpipe.


### Create the Flowpipe Integration

Now that the app is created, we will create a new Slack integration in Flowpipe.  If you don't already have one, create a `flowpipe.fpc` in your config directory (usually `~/.flowpipe/config/`).  Create a [Slack Integration](/docs/reference/config-files/integration/slack).  Set the `token` and `signing_secret` to the values for your slack app, and set the channel to whichever Slack channel you would like to use as the default channel.

```hcl
integration "slack" "default" {
  token           = "xoxb-111111"
  channel         = "#vandelay-industries"
  signing_secret  = "000000000000"
}
```

Because Slack will need to call back to Flowpipe, we will need a public endpoint. For this example, we will use [ngrok](https://ngrok.com/). Download and install ngrok, and then start it, listening to HTTP on the Flowpipe port:
```bash
ngrok http 7103
```

When ngrok starts, it will create a random public URL to forward to your local system.  Copy this URL (from the `Forwarding` line), we will use it to set the Flowpipe base URL.  In this case, we are using an ephemeral address from ngrok, so we will just pass the base URL to the `flowpipe server` command.

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
Request URL: https://0000-1111-2222-ee0-16b0-91dd-fdc0-3333-4444.ngrok-free.app/api/latest/integration/slack/default/0s9bkjhq8mn0x
```

### Enable Interactivity in the Slack App

Now we need to enable your Slack app for interactivity and complete the Slack setup.
1. In a browser, go to the [Slack app configuration](https://api.slack.com/apps) page for the app that you created earlier.
1. In the settings for the app, go to **Interactivity & Shortcuts**.  Enable **Interactivity**, and set the **Request URL** to the integration's `Request URL`.  Click **Save Changes**.
1. Re-install the app in the workspace.  From the menu on the left, select **Install App**, then click **Reinstall to Workspace**.


The Slack app is now configured.  **Note that you must also invite the bot user to any channel that you want to post messages to!**  You can go to the channel in Slack and invite the integration by name: `/invite @Flowpipe Integration`.


### Setup the Notifier

Now that the app is set up and installed in your Slack workspace and the `integration` is set up in Flowpipe, you can add the integration to your [default notifier](/docs/reference/config-files/notifier) (or create a new one) by adding this to your `~/.flowpipe/config/flowpipe.fpc` file:

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