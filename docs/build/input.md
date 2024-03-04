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

This pipeline will prompt the user for approval using `Yes` and `No` buttons (there are other [input step types](/docs/flowpipe-hcl/step/input#input-types), such as `text`, `select`, and `multiselect`).
After the user clicks a button, the pipeline will continue, and we will return the selected button value in the `choice` output.  This is a trivial example, of course; usually, you will use the input's `value` in a subsequent step.

The `input` step routes the request to an [integration](/docs/reference/config-files/integration) via a [notifier](/docs/reference/config-files/notifier). You don't need to create these to get started though;  Flowpipe creates a default [http integration](/docs/reference/config-files/integration/http) as well as a [default notifier](/docs/reference/config-files/notifier#default-notifier) that routes to it. 

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
[flowpipe] Execution ID: exec_cnb4aobjtojdrgh3gakg
[input_step_example_01] Starting pipeline
[input_step_example_01.approval] Starting input: http://localhost:7103/webform/input/rgh3gam0/3bd5115691383e3f2a95532f3db02f5b8ca02cde802e302b8a9008fad9637220
```

Open the URL in your browser.  The form appears.  

![](/images/docs/build/input_webform_approval.png)


Click one of the buttons, then return to the terminal where you are running the pipeline.  You will see that the pipeline is now finished, and your choice was returned in the output:

```bash
[flowpipe] Execution ID: exec_cnb4aobjtojdrgh3gakg
[input_step_example_01] Starting pipeline
[input_step_example_01.approval] Starting input: http://localhost:7103/webform/input/rgh3gam0/3bd5115691383e3f2a95532f3db02f5b8ca02cde802e302b8a9008fad9637220
[input_step_example_01.approval] Complete 7m14s
[input_step_example_01] Output choice = Approved
[input_step_example_01] Complete 7m14s exec_cnb4aobjtojdrgh3gakg
```



## Create an Integration

While the default `http` integration is useful for testing pipelines, you probably want to send input requests to other people via other mechanisms, like [Email](/docs/reference/config-files/integration/email) or [Slack](/docs/reference/config-files/integration/slack).

The `integration` block allows you to define configuration information for communicating with an external system such as Slack or Email.  `integration` blocks are *configuration* resources - they are defined in configuration files (`.fpc`), not mod files(`.fp`).

Set up a [Slack integration](/docs/reference/config-files/integration/slack#setting-up-as-slack-integration) or an [Email integration](/docs/reference/config-files/integration/email) to send messages and input requests via Slack or Email.


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


![](/images/docs/build/input_slack_approval.png)


