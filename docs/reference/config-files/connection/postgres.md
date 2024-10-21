---
title:  postgres
sidebar_label: postgres
---

# postgres

The `postgres` connection can be used to access a PostgreSQL database.

```hcl
connection "postgres" "my_connection" {
   host     = "localhost"
   port     = 5432
   db       = "mydb"
   username = "myuser"
   password = "mypassword123"
}
```

## Arguments

| Name                | Type    | Required?| Description
|---------------------|---------|----------|-------------------
| `connection_string` |  String | Optional | Full PostgreSQL connection string.
| `db`                |  String | Optional | Database name.  Defaults to `$PGDATABASE`.
| `host`              |  String | Optional | Database hostname.  Defaults to `$PGHOST`.
| `password`          |  String | Optional | Database password. Defaults to `$PGPASSWORD`
| `port`              |  Number | Optional | Database port.  Defaults to `$PGPORT`.
| `ssl_mode`          |  String | Optional | PostgreSQL [SSL Mode](https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-PROTECTION), one of  `disable`, `allow`, `prefer`, `require`, `verify-ca`, `verify-full`.  The defaults to `$PGSSLNEGOTIATION`.
| `username`          |  String | Optional |  Database username. Defaults to `$PGUSER`.


All arguments are optional, and a `postgres` connection with no arguments will behave the same as the [default connection](#default-connection).

<!--

| `search_path`       |  String | Optional | Database search path.
| `search_path_prefix`|  String | Optional | Database search path prefix.

-->



All arguments are optional, and a `postgres` connection with no arguments will behave the same as the [default connection](#default-connection).

Typically, you supply the `host`, `port`, `db`, `username`, and `password`:

```hcl
connection "postgres" "my_connection" {
   host     = "localhost"
   port     = 5432
   db       = "mydb"
   username = "myuser"
   password = "mypassword123"
}
```

but you may specify a `connection_string` instead:

```hcl
connection "postgres" "postgres_connection" {
  connection_string = "postgres://myuser:mypassword123@localhost:5432/mydb"
}
```


In either case, the `connection_string` is returned as an attribute.  Regardless of the syntax used to define the connection, you can get its connection_string at run time:

```hcl
pipeline "example" {
   output "connection_string" {
      value = connection.postgres.default.connection_string
   }
}
```

## Default Connection

The `postgres` connection type includes an implicit, default connection (`connection.postgres.default`) that will be configured using the [libpq environment variables](https://www.postgresql.org/docs/current/libpq-envars.html).


```hcl
connection "postgres" "default" {
  db       = env("PGDATABASE")
  host     = env("PGHOST")
  password = env("PGPASSWORD")
  port     = env("PGPORT")
  ssl_mode = env("PGSSLNEGOTIATION")
  username = env("PGUSER")
}
```