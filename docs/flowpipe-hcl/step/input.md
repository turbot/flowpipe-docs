---
title: input
sidebar_label: input
---

# input


## Overview
<!-- 
Flowpipe allows you to optimize and automate your operational and business processes, but there are times when human interaction is required.  For example, a manager may be required to approve a privilege escalation request.  Furthermore, we live in an era of constant communication across multiple channels.  People prefer to use specific tools for communication and expect these interactions to occur in their tools.  The Flowpipe `input` step primitive provides the ability to prompt for human input via multiple channels (slack, email, teams, etc) and wait for a response.  



To facilitate interacting with multiple delivery mechanisms, flowpipe also includes an `integration` configuration resource.  This allows you to configure flowpipe for 2-way communication with your tool (slack, email, etc).  `integration` is a configuration resource (defined in `.fpc` files) like `credential`, thus the configuration is installation level, not mod level; you can define the integrations for your installation once and refer to them from any mods.

Sending notifications is a common pattern, and often users will want to route a request to more than one user, group, or channel, and via more than one delivery mechanism.  For instance, you may want to request approval via slack AND email.  The `notifier` resource allows you to define a list notification integrations to send notifications to.  

-->




-----
## Input Step

The `input` step primitive allows a workflow to prompt for user input.  The pipeline will send the prompt to one or more configured integrations and will then pause and wait for a response.  When a response is received, the pipeline will wake up and continue.

An `input` step is limited to a single prompt and a single input element - It asks a single question, and returns a single answer (`value`).


There are multiple [input types](#input-types), but they all behave roughly the same
  - generally, you specify `option` blocks, 
    - options have optional `label`, else use block label
    - select types may have `selected` boolean
  - you may either specify `option` blocks OR `options` which is a list of objects
    - the list of objects format allows you to build the list is built dynamically

You must select a [notifier](#notifiers) to send the request to.  The notifier dictates which integration to use to send the the request (Slack, email, etc) as well as routing information (which slack channel which recipient, etc.)



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



### Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `notifier`      | Notifier Reference | Required    | The [notifier](#notifiers) to send the request to.
| `cc`            | List of Strings | Optional    | The email addresses to send to. This only applies to  notifiers that uses `email` integrations.
| `bcc`           | List of Strings | Optional    | The email addresses to send to. This only applies to  notifiers that uses `email` integrations.
| `channel`       | String    | Optional    | The channel to send the request to.  This only applies to  notifiers that uses `slack` integrations.
| `depends_on`    | List of Steps | Optional | A list of steps that this step depends on
| `description`   | String    | Optional    | A description of the step.
| `for_each`      | Map or List | Optional  | A map or list used as a [step iterator](#iteration).  A step instance will be created for each item in the map or list.
| `if`            | Condition | Optional    | A [condition to evaluate to determine whether to run this step](#conditional-execution). The step will run only if this condition evaluates to `true`.
| `option`        | Block     | Optional    | The available [options](#options) to present to the user as `option` blocks.  You may either specify one or ore `option` blocks or a single `options` list, but not both.
| `options`       | List      | Optional    | The available [options](#options) to present to the user as a list of objects.  You may either specify one or more `option` blocks or a single `options` list, but not both.
| `prompt`        | String    | Optional    | The text to present to the user as a prompt
| `subject`       | String    | Optional     | A brief overview statement used by notifiers,  For `email` integrations, this will be used as the email subject. For Slack integrations and web format, this a message title.
| `title`         | String    | Optional    | Display title for the step.
| `timeout`       | Number	  | Optional	  | Amount of time your container has to run in seconds. Defaults to `60`.
| `to`            | List of Strings | Optional    | The email addresses to send to. This only applies to  notifiers that uses `email` integrations.
| `type`          | String    | Required     | The [input type](#input-types) to present to the user



### Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `value `        | String  | The value that the user has selected.  This may be a scalar value (when the type is `button`, `text`, `select`, `combo`) or a list (when the type is `multiselect`, `multicombo`)


### Input Types

| Type          | Description
|---------------|---------------------
| `button`      | Click a button
| `text`        | Enter a single line of text
| `select`      | Select a single item from a dropdown list
| `multiselect`	| Select one or more items from a dropdown list
| `combo`       | Select a single item from a dropdown list, or enter a new value
| `multicombo`	| Select one or more items from a dropdown list, or enter new values


The input names and structure are consistent with [steampipe/powerpipe inputs](https://steampipe.io/docs/reference/mod-resources/input).


#### Button - Simple 

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

#### Button - With labels and values 

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

#### select or combo - basic

```hcl
pipeline "my_pipe" {

  step "input" "select_region" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-2" {}    
    option "us-west-2" {}
    
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets
    region   = step.input.select_region.value
  }
}
```

#### select or combo - with labels, default selection

```hcl
pipeline "my_pipe" {

  step "input" "select_region" {
    notifier = notifier.default
    type     = "combo"  
    prompt   = "Select a region:"

    option "us-east-1" {
      label     = "N. Virginia"
      selected  = true
    }
    option "us-east-2" {
      label     = "Ohio"
    }
    option "us-west-2" {
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

#### multiselect or multicombo - simple

(multi-select combo has the same format)

```hcl
pipeline "my_pipe" {

  step "input" "select_regions" {
    notifier = notifier.default
    type     = "multicombo"  
    prompt   = "Select regions:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-2" {}    
    option "us-west-2" {}
  }

  step "pipeline" "list_buckets" {
    pipeline = aws.pipeline.list_buckets

    for_each = step.input.select_regions.value
    region   = each.value
  }

}
```

#### multiselect or multicombo - with labels, default selection

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

    option "us-west-2" {
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



### Options

The available options to present to the user are specified in either `option` blocks, or in the `options` list.  You may either specify one or ore `option` blocks or a single `options` list, but not both.

If no arguments are passed to `option`, then the block label is used as both the `label` (the text to display) and `value` (the value to return if this option is selected):

```hcl
  step "input" "select_region" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"

    option "us-east-1" {}
    option "us-east-2" {}
    option "us-west-2" {}    
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
    option "us-west-2" {
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
    sql = "select name as value from aws_region where opt_in_status <> 'not-opted-in' order by name" 
  }

  step "input" "select_regions" {
    notifier = notifier.default
    type     = "select"   
    prompt   = "Select a region:"
    options  = step.query.get_regions.rows
  }
}
```

#### Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `label`         | String    | Optional    | The text to display for the option.
| `value`         | String    | Optional    | The value to return when the option is selected
| `selected`      | Boolean   | Optional    | Set to `true` to pre-select the option



