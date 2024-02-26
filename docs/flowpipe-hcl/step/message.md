---
title: message
sidebar_label: message
---

# message

The `message` step is used to send a message to an [integration](/docs/reference/config-files/integration) via a [notifier](/docs/reference/config-files/notifier).


```hcl
pipeline "send_message" {
  step "message" "send_a_message" {

    notifier = notifier.default
    channel  = "#ops"
    to       = ["ops@bluth.com"]
    subject  = "Sample message"
    body     = "I'm a sample _markdown_ message."
  }
}
```


## Arguments
| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `body`        | String       | Required    | The body of the text to send to the user, as markdown or plain text (depending on the `markdown` argument).
| `notifier`      | Notifier Reference | Required    | The [notifier](/docs/reference/config-files/notifier) to send the request to send the message.
| `cc`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to `email` integrations.
| `bcc`           | List&ltString&gt | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to.  This only applies to  `slack` integrations.
| `markdown`      | Boolean    | Optional    | If set to `true`, the `body` will be formatted as markdown.  Set to `false` for plain text.  Defaults to `true`.
| `subject`       | String    | Optional     | The email subject.  This only applies to notifiers that uses `email` integrations.
| `to`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to  `email` integrations.


This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).
