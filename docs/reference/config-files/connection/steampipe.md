---
title:  steampipe
sidebar_label: steampipe
---

# steampipe

The `steampipe` connection can be used to access a [Steampipe](https://steampipe.io/) database.

```hcl
connection "steampipe" "steampipe_connection" {
   host     = "localhost"
   port     = 9193
   db       = "steampipe"
   username = "steampipe"
   password = "mypassword123"
}
```

## Arguments

| Name                | Type    | Required?| Description
|---------------------|---------|----------|-------------------
| `connection_string` |  String | Optional | Full PostgreSQL connection string.  Defaults to `postgres://steampipe@127.0.0.1:9193/steampipe`
| `db`                |  String | Optional | Database name.  Defaults to `steampipe`.
| `host`              |  String | Optional | Database hostname.  Defaults to `127.0.0.1`.
| `password`          |  String | Optional | Database password.
| `port`              |  Number | Optional | Database port.  Defaults to `9193`.
| `search_path`       |  String | Optional | Database search path.
| `search_path_prefix`|  String | Optional | Database search path prefix.
| `ssl_mode`          |  String | Optional | PostgreSQL [SSL Mode](https://www.postgresql.org/docs/current/libpq-ssl.html#LIBPQ-SSL-PROTECTION), one of `disable`, `allow`, `prefer`, `require`, `verify-ca`, `verify-full`.  The default is `require`.
| `username`          |  String | Optional |  Database username. Default is `steampipe`.


All arguments are optional, and a `steampipe` connection with no arguments will behave the same as the [default connection](#default-connection).

Typically, you supply the `host`, `port`, `db`, `username`, and `password`:

```hcl
connection "steampipe" "steampipe_connection" {
   host     = "localhost"
   port     = 9193
   db       = "steampipe"
   username = "steampipe"
   password = "mypassword123"
}
```

but you may specify a `connection_string` instead:

```hcl
connection "steampipe" "steampipe_connection" {
  connection_string = "postgres://steampipe:mypassword123@127.0.0.1:9193/steampipe"
}
```


In either case, the `connection_string` is returned as an attribute.  Regardless of the syntax used to define the connection, you can get its connection_string at run time:

```hcl
pipeline "example" {
   output "connection_string" {
      value = connection.steampipe.default.connection_string
   }
}
```

## Default Connection

The `steampipe` connection type includes an implicit, default connection (`connection.steampipe.default`) that will be configured to use the local Steampipe instance, eg:

```hcl
connection "steampipe" "default" {
  connection_string = "postgres://steampipe@127.0.0.1:9193/steampipe"
}
```