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

To prompt for input via Slack you need to 
- [Create an app in Slack](/docs/build/input#create-the-slack-app)
- [Create a Slack `integration`](/docs/build/input#create-the-flowpipe-integration),
- [Enable Interactivity in the Slack App](/docs/build/input#enable-interactivity-in-the-slack-app)
- [Add the integration to a `notifier`](/docs/build/input#setup-the-notifier)
- [Configure your `input` step to send to the notifier](/docs/build/input#adding-an-input-step-to-your-pipeline)  

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