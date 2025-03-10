---
title: message
sidebar_label: message
---

# message

Use the `message` step to send a one-way notification message from your pipeline.

When you run a pipeline in [client-mode](/docs/run#operating-modes), notifications for `message` steps will only appear on the command line.  

In [server-mode](/docs/run/server), notifications for `message` steps are not sent to the console.  Instead, they are sent to [integrations](/docs/reference/config-files/integration/) such as Slack or Email via a [notifier](/docs/reference/config-files/notifier), allowing you to create collaborative workflows that integrate with your preferred communication channels.


```hcl
pipeline "send_message" {
  step "message" "send_a_message" {

    notifier = notifier.default
    channel  = "#ops"
    to       = ["ops@bluth.com"]
    subject  = "Sample message"
    text     = "I'm a sample message."
  }
}
```


## Arguments
| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `text`        | String       | Required    | The message text to send to the integration.
| `notifier`      | Notifier Reference | Required    | The [notifier](/docs/reference/config-files/notifier) to send the request to send the message.
| `cc`            | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to `email` integrations.
| `bcc`           | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to.  This only applies to  `slack` integrations.
| `subject`       | String    | Optional     | The email subject.  This only applies to notifiers that uses `email` integrations.
| `to`            | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to  `email` integrations.


This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).
