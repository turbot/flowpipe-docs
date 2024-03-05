---
title:  notifier
sidebar_label: notifier
---

# Notifiers

Notifiers allow you to create a named list of notification targets, linking `message` and `input` steps to `integrations`. Like `integration`, `notifier` blocks are installation-level configuration items, defined in `.fpc` files (typically, in `~/.flowpipe/config/flowpipe.spc`).

Each notifier contains a list of integrations and related settings used to send and receive data to one or more integrations when an `input` step executes.

```hcl
notifier "admins" {

  notify {
    integration = integration.slack.default 
    channel     = "#admins"
  }
  notify {
    integration = integration.email.default 
    to          = ["admins@bluthenterprises.com"]
  }
}
```

You can have many named `notifier` instances that use the same `integration` but override specific values, to route `input` requests to different people or channels:

```hcl
notifier "admins" {

  notify {
    integration = integration.slack.default 
    channel     = "#admins"
  }
  notify {
    integration = integration.email.default 
    to          = ["admins@bluthenterprises.com"]
  }
}

notifier "hr" {

  notify {
    integration = integration.slack.default 
    channel     = "#hr"
  }
  notify {
    integration = integration.email.default 
    to          = ["hr@bluthenterprises.com"]
  }
}
```

## Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `notify`        | Block     | Required    | one of more [notify blocks](#notify-block) to send the request to.

## Notify Block

### Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `integration`   | Integration Reference | Required    | The [integration](/docs/reference/config-files/integration) to send the request to.
| `cc`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to `email` integrations.
| `bcc`           | List&ltString&gt | Optional    | The email addresses to send to. This only applies to `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to. This only applies to `slack` integrations.
| `description`   | String    | Optional    | A description of the notifier.
| `subject`       | String | Optional     | The email subject. This only applies to `email` integrations.
| `title`         | String    | Optional    | Display title for the notifier.
| `to`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to `email` integrations.

### Overriding arguments

There are some settings, such as `channel`, `to`, `cc`, `bcc`, and `subject` that can be set in `integration`, the `notifier`, or the `input` or `message` step:

1. The argument passed to the step has the highest priority
2. The argument passed to the integration has the lowest priority.

This allows you to set default message routing parameters in the `integration` but override them in the `notifier` and `step`. Consider the following example:

```hcl
integration "email" "default" {
  smtp_host = "smtp.gmail.com"
  from      = "flowpipe@bluthenterprises.com"
  to        = ["support@bluthenterprises.com"]
  subject   = "Flowpipe: Action Requested"
}

notifier "admins" {
  notify {
    integration = integration.email.default 
    to          = ["admins@bluthenterprises.com"]
    cc          = ["gob@bluthenterprises.com"]
  }
}

pipeline "change_request" {
  step "input" "my_step" {
    notifier = notifier["admins"]
    cc       = ["michael@bluthenterprises.com"]
    type     = "button"
    prompt   = "Do you want to approve?"

    option "Approve" {}
    option "Deny" {}
  }
}
```

When the `change_request` pipeline is run, the `input` will send the approval email to `admins@bluthenterprises.com` and will CC `michael@bluthenterprises.com`.

### Default Notifier

By default, Flowpipe will include a default `notifier` group that only includes the HTTP integration and thus works out of the box. It is equivalent to:

```hcl
integration "http" "default" {}

notifier "default" {
  notify {
    integration = integration.http.default
  }
}
```

Note that you can override the default notifier by creating a notifier named `default`. This allows you to route messages and inputs to Slack, email, etc by default.

### Using Notifiers in Mods

Notifiers are defined in config files (not mod files), but Flowpipe makes them available to mods using the `notifier` variable. This special variable is a map of all notifiers by name, so you can reference them with standard HCL syntax: `notifier.name` or `notifier[name]`.

You can reference them directly in a mod:

```hcl
step "input" "my_step" {
  notifier = notifier["admins"]
  type     = "button"
  prompt   = "Do you want to approve?"

  option "Approve" {}
  option "Deny" {}
}
```

Generally, Mods that use `input` and `message` steps should allow passing a notifier name as a parameter, but default to the [default notifier](#default-notifier):

```hcl

pipeline "my_pipe" {
  param "notify" {
    default = "default
  }

  step "input" "my_step" {
    type     = "button"
    prompt   = "Do you want to approve?"
    notifier = notifier[param.notify]
    channel  = "#general" 
    to       = ["bob.loblaw@bobloblawlaw.com"]
    subject  = "Input Requested"

    option "Approve" {}
    option "Deny" {}
  }
}
```

This approach allows the mod to work the box, but also allows users of your mod to specify a `notifier` specific to them if they desire:

```bash
flowpipe pipeline run my_pipe --arg notify=admins
```
