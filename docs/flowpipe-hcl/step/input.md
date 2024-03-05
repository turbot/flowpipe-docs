---
title: input
sidebar_label: input
---

# input

Use the `input` step primitive to prompt for user input.  The pipeline will send the prompt to one or more [integrations](/docs/reference/config-files/integration) via a [notifier](/docs/reference/config-files/notifier).  It will then pause and wait for a response.  When a response is received, the pipeline will continue and the step will return the selected `value`.

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
| `cc`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `bcc`           | List&ltString&gt | Optional    | The email addresses to send to. This only applies to notifiers that uses `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to.  This only applies to  `slack` integrations.
| `option`        | Block     | Optional    | The available [options](#options) to present to the user as `option` blocks.  You may either specify one or ore `option` blocks or a single `options` list, but not both.
| `options`       | List      | Optional    | The available [options](#options) to present to the user as a list of objects.  You may either specify one or more `option` blocks or a single `options` list, but not both.
| `prompt`        | String    | Optional    | The text to present to the user as a prompt
| `subject`       | String    | Optional     | The email subject.  This only applies to notifiers that uses `email` integrations.
| `to`            | List&ltString&gt | Optional    | The email addresses to send to. This only applies to  `email` integrations.
| `type`          | String    | Required     | The [input type](#input-types) to present to the user


This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).


## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `value `        | String  | The value that the user has selected.  This may be a scalar value (when the type is `button`, `text`, `select`) or a list (when the type is `multiselect`).


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

### Button - Simple 

```hcl
pipeline "my_pipe" {

  step "input" "my_step" {
    notifier = notifier.default
    type     = "button"
    prompt   = "do you want to approve?"

    option "Approve" {}
    option "Deny" {}

  }

  step "pipeline" "do_the_thing" {
    pipeline = pipeline.something
    if       = step.input.my_step.value == "Approve"
  }
}
```

### Button - With labels and values 

```hcl
pipeline "my_pipe" {

  step "input" "my_step" {
    notifier = notifier.default
    type     = "button"
    prompt   = "do you want to approve?"

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

### select - basic

```hcl
pipeline "my_pipe" {

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
    region   = step.input.select_region.value
  }
}
```

### select - with labels & default selection

```hcl
pipeline "my_pipe" {

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
    region   = step.input.select_region.value
  }

}
```

### multiselect  - basic


```hcl
pipeline "my_pipe" {

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
    region   = each.value
  }

}
```

### multiselect with labels & default selection

```hcl
pipeline "my_pipe" {

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
    region   = each.value
  }

}
```



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
        value     = "us-east-2
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
    prompt   = "do you want to approve?"

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


