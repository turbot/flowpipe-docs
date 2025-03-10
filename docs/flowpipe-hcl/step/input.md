---
title: input
sidebar_label: input
---

# input

Use the `input` step primitive to prompt for user input.  When the pipeline reaches an `input` step, it will prompt the user for input and then pause and wait for a response.  When a response is received, the pipeline will continue and the step will return the selected `value`.

When you run a pipeline in [client-mode](/docs/run#operating-modes), notifications for `input` steps will only appear on the command line.  

In [server-mode](/docs/run/server), notifications for `input` steps are not sent to the console.  Instead, they are sent to [integrations](/docs/reference/config-files/integration/) such as Slack or Email via a [notifier](/docs/reference/config-files/notifier), allowing you to create collaborative workflows that integrate with your preferred communication channels.


```hcl
pipeline "my_step" {

  step "input" "my_step" {
    type   = "button"
    prompt = "Do you want to approve?"

    option "Approve" {}
    option "Deny" {}

    notifier = notifier.infosec
  }

  step "pipeline" "do_the_thing" {
    pipeline = pipeline.something
    if       = step.input.my_step.value == "Approve"
  }
}
```

An `input` step is limited to a single prompt and a single input element. It asks a single question and returns a single answer (`value`).

You must select a [notifier](/docs/reference/config-files/notifier) that defines which [integration](/docs/reference/config-files/integration) will handle the interaction: Slack, email, or other.

There are multiple [input types](#input-types), but they all share these traits:
- There can be one or more [options](#options).
- The options may either be specified in `option` blocks or as a list of `options`.  The block format is typically preferred when the list of options is statically defined, and the list format is useful when generating the options dynamically.
- Each option must have a `value`.
- An option may have a `label` (display value).   If no `label` is specified, the `value` is used as the label.
- An `option` may be `selected` by default.  


## Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `notifier`      | Notifier Reference | Required    | The [notifier](/docs/reference/config-files/notifier) to send the request to send the message.
| `cc`            | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `bcc`           | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to.  This only applies to  `slack` integrations.
| `option`        | Block     | Optional    | The available [options](#options) to present to the user as `option` blocks.  You may either specify one or more `option` blocks or a single `options` list, but not both.
| `options`       | List      | Optional    | The available [options](#options) to present to the user as a list of objects.  You may either specify one or more `option` blocks or a single `options` list, but not both.
| `prompt`        | String    | Optional    | The text to present to the user as a prompt
| `subject`       | String    | Optional     | The email subject.  This only applies to notifiers that uses `email` integrations.
| `to`            | List&lt;String&gt; | Optional    | The email addresses to send to. This only applies to  `email` integrations.
| `type`          | String    | Required     | The [input type](#input-types) to present to the user


This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).


## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `value `        | String  | The value that the user has selected.  This may be a scalar value (when the type is `button`, `text`, `select`) or a list (when the type is `multiselect`).

----

## Input Types

| Type          | Description
|---------------|---------------------
| `button`      | Click a button
| `text`        | Enter a single line of text
| `select`      | Select a single item from a dropdown list
| `multiselect`	| Select one or more items from a dropdown list

<!--
| `combo`       | Select a single item from a dropdown list, or enter a new value
| `multicombo`	| Select one or more items from a dropdown list, or enter new values


-->

----
### Button - Simple 

##### Console (Client-Mode)
![](/flowpipe-hcl/input_button_simple_console.png)


##### HTTP
![](/flowpipe-hcl/input_button_simple_http.png)

##### Slack
![](/flowpipe-hcl/input_button_simple_slack.png)


```hcl
pipeline "my_pipe" {

  step "input" "my_step" {
    notifier = notifier.default
    type     = "button"
    prompt   = "Do you want to approve?"

    option "Approve" {}
    option "Deny" {}

  }

  step "pipeline" "do_the_thing" {
    pipeline = pipeline.something
    if       = step.input.my_step.value == "Approve"
  }
}
```

----

### Button - With labels and values 

##### Console (Client-Mode)
![](/flowpipe-hcl/input_button_simple_console.png)

##### HTTP
![](/flowpipe-hcl/input_button_simple_http.png)

##### Slack
![](/flowpipe-hcl/input_button_simple_slack.png)


```hcl
pipeline "my_pipe" {

  step "input" "my_step" {
    notifier = notifier.default
    type     = "button"
    prompt   = "Do you want to approve?"

    option "approve_button" {
      label = "Approve"
      value = "approve_button_pressed"  
    }

    option "deny_button" {
      label = "Deny"
      value = "deny_button_pressed"
    }
  }

  step "pipeline" "do_the_thing" {
    pipeline = pipeline.something
    if       = step.input.my_step.value == "approve_button_pressed"
  }
}

```

----

### Select - basic

##### Console (Client-Mode)
![](/flowpipe-hcl/input_select_simple_console.png)


##### HTTP
![](/flowpipe-hcl/input_select_simple_http_open.png)

##### Slack
![](/flowpipe-hcl/input_select_simple_slack_open.png)


```hcl
pipeline "my_select" {

  step "input" "select_region" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-1" {}    
    option "us-west-2" {}
    
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets
    args = {
      region = step.input.select_region.value
    }
  }
}
```

----



#### Select - with labels & default selection

##### Console (Client-Mode)
![](/flowpipe-hcl/input_select_labels_console.png)

##### HTTP
![](/flowpipe-hcl/input_select_labels_http_open.png)

##### Slack
![](/flowpipe-hcl/input_select_labels_slack_open.png)


```hcl
pipeline "my_select_labels" {

  step "input" "select_region" {
    notifier = notifier.default
    type     = "select"  
    prompt   = "Select a region:"

    option "us-east-1" {
      label     = "N. Virginia"
      selected  = true
    }
    option "us-east-2" {
      label     = "Ohio"
    }
    option "us-west-1" {
      label     = "N. California"
    }    
    option "us-west-2" {
      label     = "Oregon"
    }
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets
    args = {
      region = step.input.select_region.value
    }
  }

}
```

----



### Multiselect  - basic

##### Console (Client-Mode)
![](/flowpipe-hcl/input_multiselect_simple_console.png)


##### HTTP
![](/flowpipe-hcl/input_multiselect_simple_http_selected.png)

##### Slack
![](/flowpipe-hcl/input_multiselect_simple_slack_selected.png)


```hcl
pipeline "my_multi" {

  step "input" "select_regions" {
    notifier = notifier.default
    type     = "multiselect"  
    prompt   = "Select regions:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-1" {}    
    option "us-west-2" {}
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets

    for_each = step.input.select_regions.value
    args = {
      region = each.value
    }
  }

}
```

----


### Multiselect with labels & default selection

##### Console (Client-Mode)
![](/flowpipe-hcl/input_multiselect_labels_console_selected.png)


##### HTTP
![](/flowpipe-hcl/input_multiselect_labels_http_selected.png)

##### Slack
![](/flowpipe-hcl/input_multiselect_labels_slack_selected.png)


```hcl
pipeline "my_multi_labels" {

  step "input" "select_regions" {
    notifier = notifier.default
    type     = "multiselect"  
    prompt   = "Select regions:"

    option "us-east-1" {
      label     = "N. Virginia"
      selected  = true
    }
    option "us-east-2" {
      label     = "Ohio"
      selected  = true
    }

    option "us-west-1" {
      label     = "N. California"
    }    
    option "us-west-2" {
      label     = "Oregon"
    }
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets

    for_each = step.input.select_regions.value
    args = {
      region = each.value
    }
  }

}
```

----


## Options

The available options to present to the user are specified in either `option` blocks, or in the `options` list.  You may either specify one or more `option` blocks or a single `options` list, but not both.

If no arguments are passed to an `option`, then the block label is used as both the `label` (the text to display) and `value` (the value to return if this option is selected):

```hcl
  step "input" "select_region" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-1" {}    
    option "us-west-2" {}
    
  }
```


Each option may optionally specify a `label` (the text to display) and a `value` (the value to return if this option is selected).  For some types, you may specify a boolean `selected` value to pre-select the item:

```hcl
 step "input" "select_region" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    option "us-east-1" {
      label     = "N. Virginia"
      selected  = true
    }
    option "us-east-2" {
      label     = "Ohio"
    }
    option "us-west-1" {
      label     = "N. California"
    }    
    option "us-west-2" {
      label     = "Oregon"
    }
  }
```

You may instead pass all options as a list in the `options` argument:


```hcl
  step "input" "select_regions" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    options = [
      {
        value     = "us-east-1"
        label     = "N. Virginia"
        selected  = true
      },
      {
        value     = "us-east-2"
        label     = "Ohio"
        selected  = true
      },
      {
        value     = "us-west-2"
        label     = "N. California"
      },   
      {
        value     = "us-west-2"
        label     = "Oregon"
      }
    ]
  }
```


The `options` list form is useful for building the options dynamically:


```hcl
pipeline "my_pipe" {

  step "query" "get_regions" {
    sql = <<-EOQ
      select
        name as value
      from
        aws_region
      where
        opt_in_status <> 'not-opted-in'
      order by
        name
    EOQ
  }

  step "input" "select_regions" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"
    options  = step.query.get_regions.rows
  }
}
```

Additionally, when using `button` input type, you can colorize the buttons by setting the `style` attribute on an `option`:

```hcl
pipeline "my_pipe" {

  step "input" "my_step" {
    notifier = notifier.default
    type     = "button"
    prompt   = "Do you want to approve?"

    option "approve_button" {
      label = "Approve"
      value = "approve_button_pressed"
      style = "ok"
    }

    option "deny_button" {
      label = "Deny"
      value = "deny_button_pressed"
      style = "alert"
    }
  }

  step "pipeline" "do_the_thing" {
    pipeline = pipeline.something
    if       = step.input.my_step.value == "approve_button_pressed"
  }
}
```


### Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `label`         | String    | Optional    | The text to display for the option.
| `value`         | String    | Optional    | The value to return when the option is selected.
| `selected`      | Boolean   | Optional    | Set to `true` to pre-select the option.
| `style`         | String    | Optional    | Set to `ok`, `alert`, `info` (default) to colorize option when used as a `button`.


