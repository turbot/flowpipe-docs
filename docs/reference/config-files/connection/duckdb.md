---
title:  duckdb
sidebar_label: duckdb
---

# duckdb

The `duckdb` connection can be used to access a [DuckDB](https://duckdb.org/) database.

```hcl
connection "duckdb" "duckdb_connection" {
   file = "my_ducks.db"
}
```

## Arguments

| Name       | Type    | Required?| Description
|------------|---------|----------|-------------------
| `filename` |  String | Optional | Path to a DuckDB database file to open. The filename is relative to the [mod location](/docs/run#mod-location)


## Attributes (Read-Only)

| Attribute           | Type   | Description
| --------------------| ------ |------------------------------------------------------------------------------
| `connection_string` | String | The connection string built from the arguments to this connection, in the format `duckdb://path/to/file`



## Default Connection

The `duckdb` connection type includes an implicit, default connection (`connection.duckdb.default`), however there is no file defined for this connection; you must override the default connection and supply a `filename` if you wish to use the DuckDB default connection:

```hcl
connection "duckcb" "default" {
   file = "./myfile.db"
}
```
