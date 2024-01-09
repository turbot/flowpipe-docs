---
title: query
sidebar_label: query
---

# query

The query trigger will execute a SQL query on a schedule and can pass row changes as an input to the defined pipeline.

```hcl
trigger "query" "expired_access_keys" {
  sql = <<EOQ
      select
          access_key_id,
          user_name,
          create_date,
          ctx ->> 'connection_name' as connection
      from
          aws_iam_access_key
      where
          create_date < now() - interval '90 days'
  EOQ

  primary_key = "access_key_id"

  capture "insert" {
    pipeline = pipeline.delete_expired_access_keys

    args = {
        access_keys = self.inserted_rows
    }
  }
}

```

You may define a [capture block](#capture) for each type of change that you wish to handle. The block label must match the CRUD operation name that you wish to handle (`insert`, `update`, or `delete`).  When the query runs, if there are any new, updated, or missing rows it will trigger a pipeline run for the relevant `capture` operations.  The `inserted_rows`, `updated_rows`, and `deleted_keys` attributes will contain the details about which rows were added, updated, or deleted since the last query run, and you will usually want to pass them as arguments to the pipeline.  You must define at least one `capture` block in a query trigger.

Flowpipe saves the data from the query trigger in a SQLite database so that it can determine which items are new, changed, or deleted.

The first time a query trigger runs, all items are considered new.  The pipeline defined in the `insert` capture will be run (if it is defined), and Flowpipe will store each row's `primary_key` and a hash of the full row data for comparison on subsequent trigger executions.

On subsequent query trigger runs:
- Any rows with a new `primary_key` are considered inserts.  The pipeline defined in the `insert` capture will be run (if it is defined).  Flowpipe will store each row's `primary_key` and a hash of the full row data for comparison on subsequent trigger executions.
- Any rows with an existing  `primary_key` who's hashed row data does not match what is stored are considered updates. The pipeline defined in the `update` capture will be run (if it is defined).   Flowpipe will save the new row data and hash, overwriting the previous row data and hash for that key.
- If a `primary_key` that was previously stored is no longer in the result set, then the row is determined to be deleted.  The pipeline defined in the `delete` capture will be run (if it is defined).   Flowpipe will delete the item with that `primary_key`;  if the an item with that key is returned in a future trigger tun, it will be considered an insert.


## Arguments


| Argument        | Type    | Optional?  | Description
|-----------------|---------|------------|-----------------
| `connection_string` | String | Required | A connection string used to connect to the database
| `sql`           | String  | Required   | A SQL query string.
| `description`   | String  | Optional   | A description of the trigger.
| `primary_key`   | String  | Optional   | Primary key to use for update vs insert detection.  If no primary key is defined, a hash of the row will be used as the key.
| `schedule`      | String  | Optional   | Schedule to run the query. This may be a named interval (`hourly`, `daily`, `weekly`) or a custom schedule in cron syntax  The default is `hourly`.
| `title`         | String  | Optional   | Display title for the trigger.



## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `deleted_rows`  | List    | A list of rows that were deleted since the last time the trigger ran.  `deleted_rows` does not return all the data for the deleted row, only its primary key.
| `inserted_rows` | List    | A list of rows that were inserted since the last time the trigger ran.
| `updated_rows`  | List    | A list of rows that were updated since the last time the trigger ran.  `updated_rows` contains the *new* row data (after it was updated).



---

## capture

You must define at least one `capture` block in a query trigger.

You may define a `capture` block for each type of change that you wish to handle. The block label must match the CRUD operation name that you wish to handle (`insert`, `update`, or `delete`).  When the query runs, if there are any new, updated, or missing rows it will trigger a pipeline run for the relevant `capture` operations.    


### Arguments

| Argument        | Type    | Optional?  | Description
|-----------------|---------|------------|-----------------
| `pipeline`      | Pipeline Reference   | Required | A reference to a `pipeline` resource to start when this trigger runs.  
| `args`	        | Map	    | Optional	 | A map of arguments to pass to the pipeline.



## More Examples

### Composite Key

The query trigger does not explicitly support composite keys, however you can concatenate multiple columns to create one.

```hcl
trigger "query" "dns_changes" {
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
        domain = 'flowpipe.io'
  EOQ

  primary_key = "composite_key"

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

  primary_key = "arn"

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

  primary_key = "arn"

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
