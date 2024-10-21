---
title:  duckdb
sidebar_label: duckdb
---

# duckdb

The `duckdb` connection can be used to access a [DuckDB](https://duckdb.org/) database.

```hcl
connection "duckdb" "duckdb_connection" {
   connection_string = "duckdb://my_ducks.db"
}
```

## Arguments

| Name                | Type    | Required?| Description
|---------------------|---------|----------|-------------------
| `connection_string` |  String | Optional | DuckDB connection string



### Connection String

The DuckDB connection string is the path to a DuckDB database file:

```bash
duckdb:path/to/file
```

The path is relative to the [mod location](/docs/run#mod-location), and `//` is optional after the scheme, thus the following are equivalent relative paths:

```hcl
duckdb:./my_ducks.db
duckdb://./my_ducks.db
duckdb://my_ducks.db
```

and these are equivalent absolute paths:

```hcl
duckdb:/var/db/my_ducks.db
duckdb:///var/db/my_ducks.db
```

All arguments are optional, and a `duckdb` connection with no arguments will behave the same as the [default connection](#default-connection).


## Default Connection

The `duckdb` connection type includes an implicit, default connection (`connection.duckdb.default`), however 

