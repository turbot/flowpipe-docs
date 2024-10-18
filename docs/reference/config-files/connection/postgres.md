---
title:  postgres
sidebar_label: postgres
---

# postgres

The `postgres` connection can be used to access a PostgreSQL database.

```hcl
connection "postgres" "postgres_connection" {
   
}
```

## Arguments

| Name                | Type    | Required?| Description
|---------------------|---------|----------|-------------------
| `connection_string` |  String | Optional |  SQL connection string

All arguments are optional, and a `postgres` connection with no arguments will behave the same as the [default connection](#default-connection).

connection_string
username
host
port
password
ssl_mode



## Attributes (Read-Only)

| Attribute       | Type   | Description
| --------------- | ------ |------------------------------------
| `???`           | String | blah


## Default Connection

The `postgres` connection type includes an implicit, default connection (`connection.postgres.default`) that will be configured using the environment variables ....

```hcl
connection "postgres" "default" {

}
```