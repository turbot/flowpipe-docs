---
title: query
sidebar_label: query
---

# query

A query step runs a database query against a database. [Postgres](#postgres-query), [MySQL](#mysql-query), [SQLite](#sqlite-query), and [DuckDB](#duckdb-query) databases are currently supported.

```hcl
pipeline "enabled_regions" {

  step "query" "get_enabled_regions" {
    database = "postgres://steampipe@localhost:9193/steampipe"
    sql      = <<-EOQ
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

| Argument   | Type   | Optional? | Description
| -----------| ------ | --------- | ---------------------------------------------------- 
| `sql`      | String | Required  | A SQL query string.
| `args`     | List    | Optional | A list of arguments to pass to the query.
| `database` | String | Optional  | The database to query. This may be a connection reference (`connection.steampipe.default`), a connection string (`postgres://steampipe@127.0.0.1:9193/steampipe`), or a Pipes workspace (`acme/anvils`). If not set, the default set in the [mod `database`](/docs/flowpipe-hcl/mod) will be used.

This step also supports the [common step arguments](/docs/flowpipe-hcl/step#common-step-arguments) and [attributes](/docs/flowpipe-hcl/step#common-step-attributes-read-only).

## Attributes (Read-Only)

| Attribute | Type            | Description                                   |
| --------- | --------------- | --------------------------------------------- |
| `rows`    | List of objects | The query results, as an array of row objects |

The `rows` attribute contains the query result as a list of objects with an item for each row, where the column name is the key and the column value is the value.

For example:

```hcl
pipeline "enabled_regions" {

  step "query" "get_enabled_regions" {
    database = "postgres://steampipe@localhost:9193/steampipe"
    sql      = <<-EOQ
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

## More Examples


### Steampipe Query

If no `database` is specified, then the default defined in the [mod `database`](/docs/flowpipe-hcl/mod) will be used.  If that is not set, the local Steampipe instance will be used by default.

You can also specify a [Steampipe connection](/docs/reference/config-files/connection/postgres) to connect to a Steampipe database:


```hcl
pipeline "instances_by_region" {
  step "query" "get_instances_by_region" {
    database = connection.steampipe.default
    sql      = "select region, count(*) from aws_ec2_instance group by region;"
  }
}
```


### Postgres Query

You can use a [Postgres connection](/docs/reference/config-files/connection/postgres) to connect to a PostgreSQL database:


```hcl
pipeline "enabled_regions" {
  step "query" "get_enabled_regions" {
    database = connection.postgres.mydb
    sql      = "select name, account_id, opt_in_status from aws_region where opt_in_status <> 'not-opted-in'"
  }
}
```

Alternatively, you can pass the connection string directly, with the standard URI syntax supported by `psql` and `pgcli`:

```bash
postgresql://[user[:password]@][host][:port][/dbname][?param1=value1&...]
```


```hcl
pipeline "enabled_regions" {
  step "query" "get_enabled_regions" {
    database = "postgres://steampipe@localhost:9193/steampipe"
    sql      = "select name, account_id, opt_in_status from aws_region where opt_in_status <> 'not-opted-in'"
  }
}
```

### SQLite query

You can use a [SQLite connection](/docs/reference/config-files/connection/sqlite) to connect to a SQLite database:


```hcl
pipeline "sqlite_query" {
  step "query" "step_1" {
    database = connection.sqlite.my_db
    sql      = "select * from my_sqlite_table;"
  }
}
```

Alternatively, pass the connection string directly, in the format `sqlite:path/to/file`:

```hcl
pipeline "sqlite_query" {
  step "query" "step_1" {
    database = "sqlite:./my_sqlite_db.db"
    sql      = "select * from my_sqlite_table;"
  }
}
```

The path is relative to the [mod location](/docs/run#mod-location), and `//` is optional after the scheme, thus the following are equivalent:

```hcl
database = "sqlite:./my_sqlite_db.db"
database = "sqlite://./my_sqlite_db.db"
database = "sqlite://my_sqlite_db.db"
```


### DuckDB query

You can use a [DuckDB connection](/docs/reference/config-files/connection/duckdb) to connect to a DuckDB database:

```hcl
pipeline "duckdb_query" {
  step "query" "step_1" {
    database = connection.duckdb.my_ducks"
    sql      = "select * from my_duckdb_table;"
  }
}
```

Or pass a connection string directly in the format `duckdb:path/to/file`:

```hcl
pipeline "duckdb_query" {
  step "query" "step_1" {
    database = "duckdb:./my_ducks.db"
    sql      = "select * from my_duckdb_table;"
  }
}
```

The path is relative to the [mod location](/docs/run#mod-location), and `//` is optional after the scheme, thus the following are equivalent:

```hcl
database = "duckdb:./my_ducks.db"
database = "duckdb://./my_ducks.db"
database = "duckdb://my_ducks.db"
```



### MySQL Query

You can use a [MySQL connection](/docs/reference/config-files/connection/mysql) to connect to a MySQL database:

```hcl
pipeline "mysql_query" {
  step "query" "step_1" {
    database = connection.mysql.default
    sql      = "select host, user from user;"
  }
}
```

Or pass a connection string directly. The MySQL connection string supports the syntax of the [GO SQL driver for MySQL](https://github.com/go-sql-driver/mysql?tab=readme-ov-file#examples):

```bash
mysql://[user[:password]@]network-location[:port][/dbname][?param1=value1&...]
```

```hcl
pipeline "mysql_query" {
  step "query" "step_1" {
    database = "mysql://root:my_pass@tcp(localhost)/mysql"
    sql      = "select host, user from user;"
  }
}
```