---
title:  Slack Integration
sidebar_label: slack
---

# Slack Integration

The `slack` integration can be used to send Slack messages to prompt for user input in an `input` step.  


```hcl
integration "slack" "my_slack_app" {
  token           = "xoxp-111111"
  channel         = "#infosec"
  signing_secret  = "Q#$$#@#$$#W"
}
```

To prompt for input via Slack you must create a Slack `integration`, add it to a [notifier](/docs/reference/config-files/notifier), and then configure your [`input` step](/docs/flowpipe-hcl/step/input) to send to the notifier.

## Arguments


| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `channel`       | String	  | Optional	  | The Slack channel to send to.  This value should be considered a default; if an `input` step specifies a `channel` it will override this value.   Note that a webhook is tied to a specific channel, so if you specify a `webhook_url` then this setting will have no effect.
| `signing_secret`| String    | Optional    | Slack signing secret used to verify that the responses are from slack.  This setting only applies if you use a `token`.
| `title`         | String    | Optional    | Display title for the integration.
| `token`         | String	  | *Optional	  | The slack user token for the app.  The token must have the required scopes. You must specify either a `token` or `webhook_url`.
| `webhook_url `  | String	  | *Optional	  | A Slack webhook url to post to a slack channel. Note that a webhook is tied to a specific channel; if you specify a channel in the step options it will be ignored. You must specify either a `token` or `webhook_url`.



## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `request_url`   | String  | The webhook URL to configure as the request url in your slack app



## Examples

Slack integration requires exactly one of:
- A slack **token**
- A **webhook_url**.  Note that a webhook is tied to a specific channel; if you specify a channel in the step options it will be ignored.



### Example: Slack with token

```hcl
integration "slack" "my_slack_app" {
  token           = "xoxp-111111"
  channel         = "#infosec"

  # optional - if you want to verify the source
  signing_secret  = "Q#$$#@#$$#W"
}
```


### Example: Slack with webhook url

```hcl
integration "slack" "my_slack_app" {
  webhook_url = "https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX"

}
```

## Setup instructions - Creating your own Slack app

1. In Slack, [create a Slack app](https://api.slack.com/start/quickstart) for Flowpipe.
    - Generate an **app-level token** or an **incoming webhook**.  Note that webhooks are limited to a single channel only.
    - If you are setting up an **app-level token**, assign the appropriate scopes (`read`, `chat:write`).

2. Creates a `slack` integration in `~/.flowpipe/flowpipe.spc`.
    - Specify the `token` or `webhook_url` from step 1.
    - Optionally set the `signing_secret` with the value for the Slack app.
    ```hcl
    integration "slack" "default" {
      token           = "xoxb-111111"
      channel         = "#infosec"
      signing_secret  = "Q#$$#@#$$#W"
    }
    ```
     
3. Set your [base URL](/docs/reference/env-vars/flowpipe_base_url) to a host that the recipient can route to. This is the base URL that Flowpipe advertises when it interacts with external systems to allow them to call back to Flowpipe.  This may include the DNS or IP address of the system on which you are running Flowpipe, or it may be a reverse proxy such as [ngrok](https://ngrok.com/).  You can set the base URL via the [FLOWPIPE_BASE_URL](/docs/reference/env-vars/flowpipe_base_url) environment variable, the `base_url` [workspace argument](/docs/reference/config-files/workspace), or pass it via the `--base-url` argument to the [flowpipe server](/docs/reference/cli/server) command.  

3. Update the Slack app to enable Enable **Interactivity**.
  - Run `flowpipe integration show` to get the `request_url`
  - In the settings for the app in Slack, go to **Interactivity & Shortcuts**.  Enable **Interactivity**, and set the **Request URL** to the integration's `request_url`.

4. Install (or re-install) the app to your Slack Workspace


Once the app and integration are set up, you will need to add the integration to your [default notifier](/docs/reference/config-files/notifier), or create a new one:
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