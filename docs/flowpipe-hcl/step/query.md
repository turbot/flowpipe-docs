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


This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).


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

Postgres `connection_string` follows the standard URI syntax supported by `psql` and `pgcli`:
```bash
postgresql://[user[:password]@][host][:port][/dbname][?param1=value1&...]
```

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

The SQLite `connection_string` is the path to a SQLite database file:
```bash
sqlite:path/to/file
```

The path is relative to the [mod location](/docs/run#mod-location), and `//` is optional after the scheme, thus the following are equivalent:
```hcl
connection_string = "sqlite:./my_sqlite_db.db"
connection_string = "sqlite://./my_sqlite_db.db"
connection_string = "sqlite://my_sqlite_db.db"
```


```hcl
pipeline "sqlite_query" {
  step "query" "step_1" {
    connection_string = "sqlite:./my_sqlite_db.db"

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

The DuckDB `connection_string` is the path to a DuckDB database file:
```bash
duckdb:path/to/file
```

The path is relative to the [mod location](/docs/run#mod-location), and `//` is optional after the scheme, thus the following are equivalent:
```hcl
connection_string = "duckdb:./my_ducks.db"
connection_string = "duckdb://./my_ducks.db"
connection_string = "duckdb://my_ducks.db"
```

```hcl
pipeline "duckdb_query" {
  step "query" "step_1" {
    connection_string = "duckdb:./my_ducks.db"

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

The MySQL connection string supports the syntax of the [GO SQL driver for MySQL](https://github.com/go-sql-driver/mysql?tab=readme-ov-file#examples):

```bash
mysql://[user[:password]@]network-location[:port][/dbname][?param1=value1&...]
```


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
