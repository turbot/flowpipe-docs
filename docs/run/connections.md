---
title: Managing Connections
sidebar_label: Connections
---

# Managing Connections

Flowpipe **Connections** provide a mechanism for defining credentials and options for interacting with external systems.

## Defining / Declaring  Connections
`connection` is a top-level resource in a config file (.fpc). Connections are defined in configuration files (`.fpc`), NOT mod files (`.fp`).  Like `workspace`, a `connection` can be defined in any .fpc file in the config directory (`~/.flowpipe/config/*.fpc`) or in the root of the `mod-location` (usually `$PWD/*.fpc`). 

While it is *possible* to add connections in `.fpc` in a mod repo, you should usually avoid doing so:
  - They often contain secrets
  - Different users will have different secrets - you should not share connections

Connections are defined in a `connection` block.  They have a type label and a name.

```hcl
connection "aws" "aws_01" {
  access_key = "ASIAQGDFAKEKGUI5MCEU"
  secret_key = "QhLNLGM5MBkXiZm2k2tfake+TduEaCkCdpCSLl6U"
}
```

There is a type for each service:  `aws`, `gcp`, `azure`, `github`, `slack`, etc...  The arguments and attributes vary by type. 

```hcl
connection "gcp" "gcp_01" {
  credentials    = "~/.config/gcloud/application_default_credentials.json"
}
connection "slack" "slack" {
  token    = "xoxp-asd7f8fd0fake9890"
}
```


You can use the `env()` function to set connections to the value of an environment variable:
```hcl
connection "slack" "default" {
  token    = env("SLACK_TOKEN")
}
```

### Default connections
Flowpipe also creates a single **implicit default** connection for each connection type.  The connection is named `default`, eg `connection.aws.default`, `connection.github.default`, etc.  The intent of the default connection is that Flowpipe can "just work" with your existing setup, and is similar to the default connection that Steampipe creates for each plugin.  For example, the AWS default connection will use the standard AWS SDK credential resolution mechanism to create a Flowpipe connection that will use the same credential that the `aws` CLI would use. 

You can override the default by simply creating a connection for that type that is named `default`:

```hcl
connection "slack" "default" {
  token    = "xoxp-mydefaulttoken"
}
```

----

## Using / Referencing Connections 

Although connections are defined in config files (not mod files), Flowpipe makes all the connections available to mods using the `connection` variable.  This special variable is a map of all connection types by type and name, so you can reference it with standard HCL syntax: `connection.{type}.{name}` eg `connection.aws.aws01` or `connection[{type}][{name}]` eg `connection["aws"["aws01"]`.

You can reference them directly in a mod:
```hcl
pipeline "ex1" {
  step "container" "aws" {
    image = "public.ecr.aws/aws-cli/aws-cli"
    cmd   = [ "s3", "ls" ]
    env   = connection.aws.aws01.env
  } 
}
```

Since they are a map, you can even loop through them:

```hcl
pipeline "ex1" {
  step "container" "aws" {
    for_each = connection.aws
    image    = "public.ecr.aws/aws-cli/aws-cli"
    cmd      = [ "s3", "ls" ]
    env      = connection.aws[each.value].env
  } 
}
```

Or reference them dynamically:
<!--
 - this can be especially useful when [importing](#importing-connections) Steampipe connections:
-->

```hcl
pipeline "ex1" {
  step "query" "get_running_instances" {
    sql = <<-EOQ
      select
        instance_id,
        region,
        account_id,
        _ctx ->> 'connection_name' as connection_name,
        instance_state,
        tags
      from
        aws_ec2_instance
      where
        instance_state == 'running'
        and tags ->> 'env' like 'dev%'
    EOQ
  }

  step "container" "stop_instances" {
    for_each = step.query.get_running_instances.rows
    image    = "public.ecr.aws/aws-cli/aws-cli"
    cmd     = [ "ec2", "stop-instances" 
                "--instance-ids", each.value["instance_id"
                "--region", each.value["region"]]
    ]
    env   = connection.aws[each.value.connection_name].env
  } 
}
```

### Passing connections
Generally, referencing connections literally by name in a mod is bad practice, especially in library mods because the details are instance-specific - You are making assumptions about what connections have been set up, including the names of those connections.  Instead, you should generally pass in the connection as a pipeline parameter. 

Flowpipe supports `connection` as a `type` for `param` and `variable`, in addition to the builtin `string`, `number`, and `bool` types.  You may define the type as `connection`, in which case the variable or param accepts any connection, or `connection.{type}` (e.g `connection.aws`) to limit the types of connections that may be passed.  

By convention, you should set the default value of the variable to the [default connection](#default-connections) so that your mod will work even if the user does not pass the argument or variable.




```hcl
pipeline "ex01" {

  param "conn" {
    type    = connection.slack
    default = connection.slack.default
  }

  step "http" "send_msg" {
    url = "https://slack.com/...."
    
    request_headers = {
      Content-Type = "application/json; charset=utf-8"
      Authorization = "Bearer ${param.conn.token}"
    }
  }

  output "results_http" {
    value = step.http.send_msg.response_body
  }

}
```

You can run the pipeline without argument to use your slack default connections:

```bash
flowpipe pipeline run ex01
```

If you want to use a different connection instead, you can pass it as an argument:

```bash
flowpipe pipeline run ex01 --arg conn=connection.slack.my_connection
```


### Dynamic Connections

Some connections support dynamic attributes that contain temporary credentials.  Cloud services like AWS and GCP provide many mechanisms for defining credentials (static keys, SSO, assumed roles, profiles,etc) but in all cases can be exchanged for a "standard" format such as an access token or AWS key pair.

For example, you can specify AWS connections in many formats:
```hcl
connection "aws" "aws_static" {
  access_key = "ASIAQGDFAKEKGUI5MCEU"
  secret_key = "QhLNLGM5MBkXiZm2k2tfake+TduEaCkCdpCSLl6U"
}

connection "aws" "aws_profile" {
  profile = "awx_prod_01"
}

```

For *all* of these cases you can resolve to an access key / secret key / session token, which are available as attributes. Additionally, the `aws` connection provides an `env` attribute which is a map of the [credential-related environment variables](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/guide_credentials_environment.html) (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`) 

When writing a library mod, you should use these dynamic attributes where possible, as it simplifies the code and minimizes the number of connection-related parameters:

```hcl
pipeline "ex1" {
  param "conn" {
    type    = connection.aws
    default = connection.aws.default
  }

  step "container" "aws" {
    image = "public.ecr.aws/aws-cli/aws-cli"
    cmd   = [ "s3", "ls" ]
    env   = param.conn.env
  } 
}
```

When using dynamic connection attributes, the credentials are resolved as late as possible.  When the *step instance* runs that uses the connection, it is fetched (from the cache if not expired) or generated.  This is important because the dynamic credentials are usually temporary and will expire and become invalid. You can tell Flowpipe how long to cache these temporary connections with the `ttl` argument.  This specifies the number of seconds to cache the connection.  Not all connections support (or require) `ttl`, and for those that do, the default value varies by type.  Note that the cache is in-memory only, and does not persist when Flowpipe stops.

```hcl
connection "gcp" " my_creds" {
  credentials = "~/.config/gcloud/application_default_credentials.json"
  ttl         = 900 # 15 minutes
}
```
