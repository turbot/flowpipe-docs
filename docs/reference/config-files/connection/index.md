---
title: connection
sidebar_label: connection
---

# Connection

Flowpipe **Connections** provide a mechanism for defining and sharing secrets in your Flowpipe environment.

```hcl
connection "aws" "aws_01" {
  profile = "aws-account-01"
}

connection "gcp" "gcp_01" {
  credentials = "~/.config/gcloud/application_default_connections.json"
}

connection "slack" "slack" {
  token = "xoxp-asd7f8fd0fake9890"
}
```

Connections are defined using the `connection` block in one or more Flowpipe config files. Flowpipe will load ALL configuration files (`*.fpc`) from every directory in the [configuration search path](/docs/reference/env-vars/flowpipe_config_path), with decreasing precedence. The set of connections is the union of all connections defined in these directories.

Each connection has a type label and a name. There is a type for each service: [aws](/docs/reference/config-files/connection/aws), [gcp](/docs/reference/config-files/connection/gcp), [azure](/docs/reference/config-files/connection/azure), [github](/docs/reference/config-files/connection/github), [slack](/docs/reference/config-files/connection/slack), etc. **The arguments and attributes vary by type.**

To learn more, see **[Managing Connections →](/docs/run/connections)**

### Default connections

Flowpipe also creates a single **implicit default** connection for each connection type. The connection is named `default`, eg `connection.aws.default`, `connection.github.default`, etc. The intent of the default connection is that flowpipe can "just work" with your existing setup, and is similar to the default connection that Steampipe creates for each plugin. For example, the AWS default connection will use the standard AWS SDK connection resolution mechanism to create a Flowpipe connection that will use the same connection that the `aws` CLI would use.

You can override the default by simply creating a connection for that type that is named `default`:

```hcl
connection "slack" "default" {
  token    = "xoxp-mydefaulttoken"
}
```

---

## Using / Referencing Connections

Although connections are defined in config files (not mod files), Flowpipe makes all the connections available to mods using the `connection` variable. This special variable is a map of all connection types by type and name, so you can reference it with standard HCL syntax: `connection.{type}.{name}` eg `connection.aws.aws01` or `connection[{type}][{name}]` eg `connection["aws"["aws01"]`.

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

### Dynamic Connections

Some connections support dynamic attributes that contain temporary connections. Cloud services like AWS and GCP provide many mechanisms for defining connections (static keys, SSO, assumed roles, profiles,etc) but in all cases can be exchanged for a "standard" format such as an access token or AWS key pair.

When using dynamic connection attributes, the connections are resolved as late as possible. When the _step instance_ runs that uses the connection, it is fetched (from the cache if not expired) or generated. This is important because the dynamic connections are usually temporary and will expire and become invalid. You can tell Flowpipe how long to cache these temporary connections with the `ttl` argument. This specifies the number of seconds to cache the connections. Not all connections support (or require) `ttl`, and for those that do, the default value varies by type. Note that the cache is in-memory only, and does not persist when Flowpipe stops.

---

## Handling secret data

At this time, Flowpipe does not encrypt the connections, either in the config files or in the event store.

Flowpipe will need the secret data in the event store as long as a pipeline is running. After a pipeline completes, however, Flowpipe will attempt to redact secrets from the event store for the completed pipeline. The `args`, `attributes`, and `data` for all events related to the pipeline will be scrubbed.

Additionally, Flowpipe will redact secrets from the CLI display and diagnostic logs.

The redaction will use pattern matching to look for known secret names (`AWS_SECRET_KEY`, `password`, `token`, etc) as well as value patterns (regular expressions for JWT tokens, API tokens, AWS keys, etc).
