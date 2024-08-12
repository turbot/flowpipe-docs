---
title: query
sidebar_label: query
---

# query

The query trigger will execute an SQL query on a schedule and can pass row changes as input to the defined pipeline. The query trigger supports the same database engines as the [query step](/docs/flowpipe-hcl/step/query).

```hcl
trigger "query" "expired_access_keys" {
  database    = "postgres://steampipe@localhost:9193/steampipe"
  primary_key = "access_key_id"
  schedule    = "daily"

  sql = <<EOQ
      select
          access_key_id,
          user_name,
          create_date,
          ctx ->> 'connection_name' as connection
      from
          aws_iam_access_key
      where
          create_date < now() - interval '90 days';
  EOQ


  capture "insert" {
    pipeline = pipeline.delete_expired_access_keys

    args = {
        access_keys = self.inserted_rows
    }
  }
}

```

You may define a [capture block](#capture) for each type of change that you wish to handle. The block label must match the CRUD operation name that you wish to handle (`insert`, `update`, or `delete`). When the query runs, if there are any new, updated, or missing rows it will trigger a pipeline run for the relevant `capture` operations. The `inserted_rows`, `updated_rows`, and `deleted_rows` attributes will contain the details about which rows were added, updated, or deleted since the last query run, and you will usually want to pass them as arguments to the pipeline. You must define at least one `capture` block in a query trigger.

Flowpipe saves the data from the query trigger in a SQLite database so that it can determine which items are new, changed, or deleted.

The first time a query trigger runs, all items are considered new. The pipeline defined in the `insert` capture will be run (if it is defined), and Flowpipe will store each row's `primary_key` and a hash of the full row data for comparison on subsequent trigger executions.

On subsequent query trigger runs:

- Any rows with a new `primary_key` are considered inserts. The pipeline defined in the `insert` capture will be run (if it is defined). Flowpipe will store each row's `primary_key` and a hash of the full row data for comparison on subsequent trigger executions.
- Any rows with an existing `primary_key` whose hashed row data does not match what is stored are considered updates. The pipeline defined in the `update` capture will be run (if it is defined). Flowpipe will save the new row data and hash, overwriting the previous row data and hash for that key.
- If a `primary_key` that was previously stored is no longer in the result set, then the row is determined to be deleted. The pipeline defined in the `delete` capture will be run (if it is defined). Flowpipe will delete the item with that `primary_key`; if an item with that key is returned in a future trigger run, it will be considered an insert.

## Arguments

| Argument      | Type    | Optional? | Description
|---------------|---------|-----------|-----------------
| `database`    | String  | Required  | A connection string used to connect to the database.
| `sql`         | String  | Required  | A SQL query string.
| `description` | String  | Optional  | A description of the trigger.
| `enabled`     | Boolean | Optional  | Enable or disable the trigger. A disabled trigger will not fire, but it will retain its history and configuration. Default is `true`.
| `param`         | Block | Optional  | A [param](#parameters) block that defines the parameters that can be passed into the trigger.
| `primary_key` | String  | Optional  | Primary key to use for update vs insert detection. If no primary key is defined, a hash of the row will be used as the key.
| `schedule`    | String  | Optional  | [Schedule](/docs/flowpipe-hcl/trigger/schedule#more-examples) to run the query. This may be a named interval (`hourly`, `daily`, `weekly`, `5m`, `10m`, `15m`, `30m`, `60m`, `1h`, `2h`, `4h`, `6h`, `12h`, `24h`) or a custom schedule in cron syntax. The default is `15m` (every 15 minutes).
| `title`       | String  | Optional  | Display title for the trigger.

## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `deleted_rows`  | List    | A list of rows that were deleted since the last time the trigger ran. `deleted_rows` does not return all the data for the deleted row, only its primary key.
| `inserted_rows` | List    | A list of rows that were inserted since the last time the trigger ran.
| `updated_rows`  | List    | A list of rows that were updated since the last time the trigger ran. `updated_rows` contains the *new* row data (after it was updated).

## Parameters

One or more `param` blocks may optionally be used in a trigger to define parameters that the trigger accepts. 

```hcl
param "url" {
  type    = string
  default = "http://api.open-notify.org/astros"
}
```

### Arguments


| Name          | Type    | Description
|---------------|---------|--------------------------
| `default`     | Any     | A value to use if no argument is passed for this parameter when the query is run.
| `description` | String  | A description of the parameter.
| `type`        | String   | The data type of the parameter: `string`, `number`, `bool`, `list`, `map`, `any` (default `any`).

---

## capture

You must define at least one `capture` block in a query trigger.

You may define a `capture` block for each type of change that you wish to handle. The block label must match the CRUD operation name that you wish to handle (`insert`, `update`, or `delete`). When the query runs, if there are any new, updated, or missing rows it will trigger a pipeline run for the relevant `capture` operations.

### Arguments

| Argument   | Type               | Optional? | Description
|------------|--------------------|-----------|-----------------
| `pipeline` | Pipeline Reference | Required  | A reference to a `pipeline` resource to start when this trigger runs.
| `args`     | Map                | Optional  | A map of arguments to pass to the pipeline.

## More Examples

### Composite Key

The query trigger does not explicitly support composite keys, however you can concatenate multiple columns to create one.

```hcl
trigger "query" "dns_changes" {
  database    = "postgres://steampipe@localhost:9193/steampipe"
  primary_key = "composite_key"

  sql = <<EOQ
      select
        concat(domain, '_', type, '_', ip, '_', target) as composite_key,
        domain,
        type,
        ip,
        target,
        value
      from
        net_dns_record
      where
        domain = 'flowpipe.io';
  EOQ

  capture "insert" {
    pipeline = pipeline.notify_new_dns_record
    args = {
        records = self.inserted_rows
    }
  }
}

```

### All capture types

You may run a different pipeline for each capture block:

```hcl
trigger "query" "my_query_trigger" {
  database    = "postgres://steampipe@localhost:9193/steampipe"
  primary_key = "arn"

  sql = <<EOQ
      select
        arn,
        instance_id,
        instance_state,
        instance_type,
        region,
        account_id
      from
        aws_ec2_instance;
  EOQ

  capture "insert" {
    pipeline = pipeline.instance_added

    args = {
      rows = self.inserted_rows
    }
  }

  capture "update" {
    pipeline = pipeline.instance_changed

    args = {
      rows = self.updated_rows
    }
  }

  capture "delete" {
    pipeline = pipeline.instance_terminated

    args = {
      rows = self.deleted_rows
    }
  }
}

```

Alternatively, you may call the same pipeline from multiple capture blocks:

```hcl
trigger "query" "my_query_trigger" {
  database    = "postgres://steampipe@localhost:9193/steampipe"
  primary_key = "arn"

  sql = <<EOQ
      select
        arn,
        instance_id,
        instance_state,
        instance_type,
        region,
        account_id
      from
        aws_ec2_instance;
  EOQ

  capture "insert" {
    pipeline = pipeline.instance_add_change_delete

    args = {
      inserted_rows = self.inserted_rows
    }
  }

  capture "update" {
    pipeline = pipeline.instance_add_change_delete

    args = {
      updated_rows = self.updated_rows
    }
  }

  capture "delete" {
    pipeline = pipeline.instance_add_change_delete

    args = {
      deleted_rows = self.deleted_rows
    }
  }
}

```
