---
title: Pass Variables
sidebar_label: Pass Variables
---

# Pass Variables

[Variables](/docs/flowpipe-hcl/variable) are module-level objects that allow you to pass values to your module at runtime.  When running Flowpipe, you can pass values on the command line or from a `.fpvars` file, and you will be prompted for any variables that have no values.

[Locals](/docs/flowpipe-hcl/locals) are internal, private variables used only *within* your mod - you cannot pass values in at runtime.

##  Input Variables

### Defining Input Variables
Flowpipe mods support input variables that are similar to [Terraform input variables](https://www.terraform.io/docs/language/values/variables.html). You declare them with a `variable` block:
```hcl
variable "instance_id" {
  type = string
}
```

The [type](#types) is optional, though it is generally recommended that you provide the type explicitly to provide clarity to the user and to avoid unexpected run time errors due to unforeseen type coercions.

You can optionally define a `default` value and `description` for the variable.  

```hcl
variable "mandatory_tag_keys" {
  type        = list(string)
  description = "A list of mandatory tag keys to check for (case sensitive)."
  default     = ["Environment", "Owner"]
}
```

If a variable does not define a `default`, the user will be prompted for its value when Flowpipe starts.  If a variable defines a `default`, the user is not prompted and the default value is used if the variable is not explicitly set.

You may also provide a `description` of the variable.  The description helps to provide information about the intent and format of the variable to the user of the mod.  The description is included when the user is prompted for a variable's value.



#### Types

Flowpipe supports the standard HCL `string`, `number`, and `bool` type primitives.

```hcl
variable "my_string" {
  type = string
}

variable "counter" {
  type = number
}

variable "force" {
  type = bool
}
```


The keyword `any` may be used to indicate that any type is acceptable.  If no type is defined, it is assumed to be `any`:

```hcl
variable "myvar" {
  type = any
}
```


Collection types (`list`, `map`, `set`, `object`) may also be used:

```hcl
variable "people" {
  type    = list(string)
  default = ["Kramer", "Elaine", "George"]
}

variable "employers" {
  type    = map(string)

  default = {
    Kramer = "Kramerica Industries"
    Elaine = "J. Peterman, Inc"
    George = "Vandelay Industries"
  }
}
```

You can even create deeply nested types:

```hcl
variable "base_tag_rules" {
  type = object({
    add           = optional(map(string))
    remove        = optional(list(string))
    remove_except = optional(list(string))
    update_keys   = optional(map(list(string)))
    update_values = optional(map(map(list(string))))
  })
  description = "Base rules to apply to resources unless overridden when merged with any provided resource-specific rules."
  default     = {
    add           = {}
    remove        = []
    remove_except = []
    update_keys   = {}
    update_values = {}
  }
}

```

In addition to the standard `string`, `number`, and `bool` primitives, Flowpipe `connection` and `notifier` config primitives may be specified. 

```hcl
variable "conn" {
  type = connection
}

variable "approver" {
  type = notifier
}
```

You can even specify a variable as a specific *type* of connection:
```hcl
variable "aws_conn" {
  type    = connection.aws
  default = connection.aws.default
}

variable "github_conn" {
  type    = connection.github
  default = connection.github.default
}
```

As with any type, collections may also be defined:

```hcl
variable "aws_connections" {
  type    = list(connection.aws)
  default = [connection.aws.default]
}

variable "approvers" {
  type    = list(notifier)
  default = ["default"]
}
```


#### Enum

Flowpipe supports an `enum` argument to provide a set of allowed values; no other values are allowed.

```hcl
variable "log_level" {
  type    = string
  default = "info"
  enum    = ["emerg", "alert", "crit", "err", "warning", "notice", "info", "debug" ]
}
```


### Using Input Variables
Variables may be referenced as `var.<NAME>`.  Variables are often used as defaults for pipeline parameters:

```hcl
pipeline "list_org_workspaces" {
  param "org" {
    type    = string
    default = var.pipes_org
  }

  step "http" "list_workspaces" {
    url    = "https://pipes.turbot.com/api/latest/org/${param.org}/workspace"
    method = "get"

    request_headers = {
      Authorization = "Bearer ${file("~/.steampipe/internal/pipes.turbot.com.tptt")}"
    }
  }

  output "workspaces" {
    value = step.http.list_workspaces.response_body.items[*].handle 
  }
}
```

### Passing Input Variables
When running Flowpipe, you can pass variables in several ways.  You can pass individual variable values on the command line with one or more `--var` arguments:

```bash
flowpipe pipeline run list_org_workspaces --var=org="my_org"
```

For `connection` or `notifier` variables, pass them by name:
```bash
flowpipe pipeline run list_org_workspaces --var conn=connection.aws.prod --var approver=notifier.admins
```


When passing list variables, they must be enclosed in single quotes:

```bash
flowpipe pipeline run check_tags --var='mandatory_tags=["Owner","Application","Environment"]' 
```

You can specify variable values in a `.fpvars` file, using HCL syntax:
```hcl
mandatory_tags = [
  "Owner",
  "Application", 
  "Environment"
] 

org = "my_org"
```

Flowpipe *automatically* reads in the file named `flowpipe.fpvars` as well as any file ending in `.auto.fpvars` from the working directory if they exist.  You can also specify a variable file by name on the command line:

```bash
flowpipe pipeline run check_tags  --var-file='tags.fpvars'
```

You may also set variable values via environment variables.  Simply prefix the Flowpipe variable name with `FP_VAR_`:

```bash
export FP_VAR_mandatory_tags='["Owner","Application", "Environment"]' 
```

If you run Flowpipe from a mod that defines input variables, and they are not set anywhere (no default, not set in a `.fpvars` file, not set with `--var` argument, not set via an environment variable) then Flowpipe will prompt you for them before running the pipeline.

Flowpipe loads variables in the following order, with later sources taking precedence over earlier ones:
1. Environment variables
1. The `flowpipe.fpvars` file, if present.
1. Any `*.auto.fpvars` files, in alphabetical order by filename.
1. Any `--var` and `--var-file` options on the command line, in the order they are provided.


### Passing Variables for Dependency Mods

A Flowpipe mod can depend on other mods, and those dependency mods may include variables that you would like to pass.  To set them, prefix the variable names with the mod name and then set them like any other variable.

You can set them in a `.fpvars` file:
```hcl
// direct dependency vars
aws_tags.mandatory_tags = ["Owner","Application","Environment"]
azure_tags.mandatory_tags = ["Owner","Application","Environment"]
```

Or pass them to the command with the `--var` argument
```bash
flowpipe pipeline run check_tags  --var 'aws_tags.mandatory_tags=["Owner","Application","Environment"]'  --var 'azure_tags.mandatory_tags=["Owner","Application","Environment"]' --var 'gcp_labels.mandatory_labels=["Owner","Application","Environment"]'
 ```

##  Local Variables
Flowpipe supports using local variables in a manner similar to [Terraform local values](https://www.terraform.io/docs/language/values/locals.html).  Unlike `variables`, locals cannot be passed in at runtime, but are useful as internal private variables.

The `locals` block defines and sets one or more local variables, using standard HCL assignment syntax.  The locals are scoped to the mod, and a mod may contain multiple `locals` blocks.  Locals may reference other values in the mod, including other local values.

A set of one or more local values can be declared in one or more `locals` blocks:
```hcl
locals {
  common_tags = {
    owner       = "dev team"
    environment = "prod"
  }
}
```

Once a local value is declared, you can reference it in expressions as `local.<NAME>`.
```hcl
pipeline "my_pipeline" {
  ...
  tags = local.common_tags
}
```
