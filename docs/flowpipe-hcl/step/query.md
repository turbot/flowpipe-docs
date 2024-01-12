---
title: query
sidebar_label: query
---

# query

A query step runs a database query against a database.  [Postgres](#postgres-query), [MySQL](#mysql-query), [SQLite](#sqlite-query), and [DuckDB](#duckdb-query) databases are currently supported.


```hcl
pipeline "enabled_regions" {

  step "query" "get_enabled_regions" {
    connection_string = "postgres://steampipe@localhost:9193/steampipe"
    sql = <<-EOQ
      select 
        name,
        account_id,
        opt_in_status 
      from 
        aws_region 
      where
        opt_in_status <> 'not-opted-in'
    EOQ

  }

  output "enabled_regions" {
    value = step.query.get_enabled_regions.rows
  }
}
```

## Arguments

| Argument        | Type    | Optional?  | Description
|-----------------|---------|------------|-----------------
| `connection_string` | String | Required | A connection string used to connect to the database.
| `sql`           | String | Required | A SQL query string. 
| `args`	        | Map	    | Optional	  | A map of arguments to pass to the query.


This step also supports the [common step arguments](/docs/flowpipe-hcl/step/index#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step/index#common-step-attributes-read-only).


## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `rows`          | List of objects | The query results, as an array of row objects

The `rows` attribute contains the query result as a list of objects with an item for each row, where the column name is the key and the column value is the value.  

For example:

```hcl
pipeline "enabled_regions" {

  step "query" "get_enabled_regions" {
    connection_string = "postgres://steampipe@localhost:9193/steampipe"
    sql = <<-EOQ
      select 
        name,
        account_id,
        opt_in_status 
      from 
        aws_region 
      where
        opt_in_status <> 'not-opted-in'
    EOQ

  }

  output "enabled_regions" {
    value = step.query.get_enabled_regions.rows
  }
}
```

Results in:
```json
{
  "enabled_regions": [
    {
      "account_id": "123456789012",
      "name": "us-east-2",
      "opt_in_status": "opt-in-not-required"
    },
    {
      "account_id": "123456789012",
      "name": "us-west-1",
      "opt_in_status": "opt-in-not-required"
    },
    {
      "account_id": "123456789012",
      "name": "ap-south-1",
      "opt_in_status": "opt-in-not-required"
    },
    {
      "account_id": "123456789012",
      "name": "us-west-2",
      "opt_in_status": "opt-in-not-required"
    },
    ...
  ]
}
```


Since `rows` is a list, you can use standard HCL `for` expressions:
```hcl
output "enabled_region_names" {
    value = [ for v in step.query.get_enabled_regions.rows : v.name ]
}
```

or splats:
```hcl
output "enabled_region_names" {
    value = step.query.get_enabled_regions.rows[*].name
}
```


to extract data.
```json
{
  "enabled_region_names": [
    "us-east-2",
    "us-west-1",
    "ap-south-1",
    "us-west-2",
    ...
  ]
}
```

##  More Examples

### Postgres Query

```hcl
pipeline "enabled_regions" {

  step "query" "get_enabled_regions" {
    connection_string = "postgres://steampipe@localhost:9193/steampipe"
    sql = <<-EOQ
      select 
        name,
        account_id,
        opt_in_status 
      from 
        aws_region 
      where
        opt_in_status <> 'not-opted-in'
    EOQ

  }

  output "enabled_regions" {
    value = step.query.get_enabled_regions.rows
  }
}
```

### SQLite query

```hcl
pipeline "sqlite_query" {
  step "query" "step_1" {
    connection_string = "sqlite://./my_sqlite_db.db"

    sql = <<EOQ
      select
        *
      from
        my_sqlite_table;
    EOQ
  }

  output "results" {
    value = step.query.step_1.rows
  }
}
```


### DuckDB query

```hcl
pipeline "duckdb_query" {
  step "query" "step_1" {
    connection_string = "duckdb://./my_ducks.db"

    sql = <<EOQ
      select
        *
      from
        my_duckdb_table;
    EOQ
  }

  output "results" {
    value = step.query.step_1.rows
  }
}
```


### MySQL Query

```hcl
pipeline "mysql_query" {

  step "query" "step_1" {
    connection_string = "mysql://root:my_pass@tcp(localhost)/mysql"

    sql = <<EOQ
      select
        host,
        user
      from
        user;
    EOQ
  }

  output "results" {
    value = step.query.step_1.rows
  }
}
```
