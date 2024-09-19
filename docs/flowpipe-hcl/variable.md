---
title: variable
sidebar_label: variable
---

# variable

[Variables](/docs/build/mod-variables#input-variables), are module-level objects that essentially act as parameters for the module.  When running Flowpipe, you can pass values on the command line, from a `.fpvars` file, or from environment variables, and you will be prompted for any variables that have no values.

You can reference variable values as `var.<NAME>`


## Example Usage
```hcl
variable "region" {
  type        = string
  description = "The name of the Region."
  default     = ""
}

variable "access_key_id" {
  type        = string
  description = "The ID for this access key."
  default     = ""
}

variable "secret_access_key" {
  type        = string
  description = "The secret key used to sign requests."
  default     = ""
}

variable "session_token" {
  type        = string
  description = "The token that users must pass to the service API to use the temporary connections."
  default     = ""
}


pipeline "describe_vpcs" {
  title       = "Describe VPCs"
  description = "Describes the specified VPCs or all VPCs."

  param "region" {
    type        = string
    description = "The name of the region."
    default     = var.region
  }

  param "access_key_id" {
    type        = string
    description = "The ID for this access key."
    default     = var.access_key_id
  }

  param "secret_access_key" {
    type        = string
    description = "The secret key used to sign requests."
    default     = var.secret_access_key
  }

  param "vpc_ids" {
    type        = list(string)
    description = "The VPC IDs."
    optional    = true
  }

  step "container" "describe_vpcs" {
    image = "amazon/aws-cli"

    cmd = concat(
      ["ec2", "describe-vpcs"],
      try(length(param.vpc_ids), 0) > 0 ? concat(["--vpc-ids"], param.vpc_ids) : []
    )

    env = {
      AWS_REGION            = param.region
      AWS_ACCESS_KEY_ID     = param.access_key_id
      AWS_SECRET_ACCESS_KEY = param.secret_access_key
    }
  }

  output "stdout" {
    description = "The standard output stream from the AWS CLI."
    value       = jsondecode(step.container.describe_vpcs.stdout)
  }

  output "stderr" {
    description = "The standard error stream from the AWS CLI."
    value       = step.container.describe_vpcs.stderr
  }
}
```

## Argument Reference
| Argument  | Type | Optional? | Description
|-|-|-|-
| `default` | Any |Optional|  A default value.  If no value is passed, the user is not prompted and the default is used. 
| `description` | String| Optional|  A description of the variable.  This text is included when the user is prompted for a variable's value.
| `enum`    | Set | Optional| A set of allowed values for the variable; no other values are allowed.
| `tags`    | Map    | Optional | A map of key:value metadata for the benchmark, used to categorize, search, and filter.  The structure is up to the mod author and varies by benchmark and provider. 
| `type`    | Type | Optional | The [variable type](#variable-types).  This may be a primitive type `string`, `number`, `bool`, `connection`, `connection.{type}`, `notifier`, or a collection type `list()`, `map()`, or `any` (default `any`). 



## Variable Types
Variables may be simple types:
- `string`
- `number`
- `bool`
- `connection` 
- a typed `connection`, eg `connection.aws`, `connection.github` , etc.
- `notifier`


Variables may also be collection types:
- `list(<TYPE>)`
- `set(<TYPE>)`
- `map(<TYPE>)`
- `object({<ATTR NAME> = <TYPE>, ... })`
- `tuple([<TYPE>, ...])`

The keyword `any` may be used to indicate that any type is acceptable 


## Enum

Flowpipe supports an `enum` argument to provide a set of allowed values; no other values are allowed.

```hcl
variable "log_level" {
  type    = string
  default = "info"
  enum    = ["emerg", "alert", "crit", "err", "warning", "notice", "info", "debug" ]
}
```