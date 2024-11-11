---
id: learn
title: Learn Tailpipe
sidebar_label: Learn Tailpipe
slug: /
---

# Learn Tailpipe

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe, you can:

- Collect logs from various sources and store them efficiently in parquet files
- Query your data using familiar SQL syntax through DuckDB
- Share collected data with your team using remote object storage
- Create filtered views of your data using schemas
- Join log data with other data sources for enriched analysis

## Install the NGINX Plugin

This tutorial uses the NGINX plugin to demonstrate collecting and analyzing web server access logs. First, [download and install Tailpipe](/downloads), and then install the plugin:

```bash
tailpipe plugin install nginx
```

## Configure Data Collection

Tailpipe uses HCL configuration files to define what data to collect. Create a file named `nginx.tpc` with the following content:

```hcl
partition "nginx_access_log" "web_servers" {
    plugin = "nginx"
    source "nginx_access_log_file" {
        log_path = "/var/log/nginx/access.log"
    }
}
```

This configuration tells Tailpipe to collect NGINX access logs from the specified log file. The configuration defines:
- A partition named "web_servers" for the "nginx_access_log" table
- The source type "nginx_access_log_file" which reads NGINX formatted logs
- The path to the log file to collect from


## Collect Data

Now let's collect the logs:

```bash
tailpipe plugin install nginx

```bash
tailpipe collect nginx_access_log.web_servers
```

This command will:
1. Read the NGINX access logs from the specified file
2. Parse and standardize the log entries
3. Store the data in parquet files organized by date
4. Update the local database with table definitions

## Query Your Data

Tailpipe provides an interactive SQL shell for analyzing your collected data. Let's look at some examples of what you can do.

### Analyze Traffic by Server

This query shows a summary of traffic for each server for a specific date:

```sql
SELECT
    tp_index as server,
    count(*) as requests,
    count(distinct remote_addr) as unique_ips,
    round(avg(bytes_sent)) as avg_bytes,
    count(CASE WHEN status = 200 THEN 1 END) as success_count,
    count(CASE WHEN status >= 500 THEN 1 END) as error_count,
    round(avg(CASE WHEN method = 'GET' THEN bytes_sent END)) as avg_get_bytes
FROM nginx_access_log
WHERE tp_date = '2024-11-01'
GROUP BY tp_index
ORDER BY requests DESC;
```

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│ server      requests    unique_ips  avg_bytes   success_c…  error_cou…  avg_get_b…   │
│────────────────────────────────────────────────────────────────────────────────────  │
│ web-01.ex…  349         346         7036        267         7           7158        │
│ web-02.ex…  327         327         6792        246         11          6815        │
│ web-03.ex…  324         322         7001        254         8           6855        │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

This shows us:
- Number of requests per server
- Count of unique IP addresses
- Average response size
- Success and error counts
- Average size of GET requests

### Time-Oriented Query

Let's look at some recent log entries:

```sql
SELECT
    tp_date,
    tp_index as server,
    remote_addr as ip,
    method,
    uri,
    status,
    bytes_sent
FROM nginx_access_log
WHERE tp_date = '2024-11-01'
LIMIT 10;
```

```
+--------------------------------------------------------------------------------------+
¦ tp_date      server           ip             method  uri             status  bytes_sent¦
¦------------------------------------------------------------------------------------  ¦
¦ 2024-11-01   web-01.example  220.50.48.32   GET     /profile/user  200     5704     ¦
¦ 2024-11-01   web-01.example  10.166.12.45   GET     /blog/post/1   200     2341     ¦
¦ 2024-11-01   web-01.example  203.0.113.10   GET     /dashboard     200     11229    ¦
¦ 2024-11-01   web-01.example  45.211.16.72   PUT     /favicon.ico   301     2770     ¦
¦ 2024-11-01   web-01.example  66.171.35.91   POST    /static/main   503     5928     ¦
¦ 2024-11-01   web-01.example  64.152.79.83   GET     /logout        200     3436     ¦
¦ 2024-11-01   web-01.example  156.25.84.12   GET     /static/main   200     12490    ¦
¦ 2024-11-01   web-01.example  78.131.22.45   GET     /static/main   200     8342     ¦
¦ 2024-11-01   web-01.example  203.0.113.10   POST    /api/v1/user   200     3123     ¦
¦ 2024-11-01   web-01.example  10.74.127.93   POST    /              200     7210     ¦
+--------------------------------------------------------------------------------------+
```

Because we specified `tp_date = '2024-11-01'`, Tailpipe only needs to read the parquet files in the corresponding date directories. Similarly, if you wanted to analyze traffic for a specific server, you could add `tp_index = 'web-01.example.com'` to your WHERE clause, and Tailpipe would only read files from that server's directory.


## Understanding Data Storage

Tailpipe uses a hive-partitioned storage structure that organizes data for efficient querying. Let's look at how data is stored:

```
+-- default
    +-- nginx_access_log
    ¦   +-- tp_partition=nginx_access_log
    ¦       +-- tp_index=web-01.example.com
    ¦       ¦   +-- tp_date=2024-11-01
    ¦       ¦       +-- file_a7d40b4a-0398-46c6-8869-dc5dd87015a0.parquet
    ¦       +-- tp_index=web-02.example.com
    ¦       ¦   +-- tp_date=2024-11-01
    ¦       ¦       +-- file_696946fa-f636-4b54-a8ec-82f64704ff50.parquet
    ¦       +-- tp_index=web-03.example.com
    ¦           +-- tp_date=2024-11-01
    ¦               +-- file_a061d992-eb86-46a5-bc8f-d1a4b2fcce25.parquet
    +-- pipes_audit_log
    ¦   +-- tp_partition=pipes_audit_log
    ¦       +-- tp_index=turbot-ops
    ¦           +-- tp_date=2024-11-05
    ¦               +-- file_ebab33de-2dcc-437c-8722-e371316f0b22.parquet
    +-- tailpipe.db
```

The structure has several key components:
- **Partition**: Groups data by source (e.g., `nginx_access_log`)
- **Index**: Sub-divides data by a meaningful key (e.g., server name for NGINX logs)
- **Date**: Further partitions data by date
- Each partition contains parquet files with the actual log data

This hierarchical structure enables efficient querying through partition pruning. When you query with conditions on `tp_partition`, `tp_index`, or `tp_date`, Tailpipe (and DuckDB) can skip reading irrelevant parquet files entirely.


### Using DuckDB Directly

Since Tailpipe stores data in standard parquet files using a hive partitioning scheme, you can query the data directly with DuckDB:

```sql
$ cd ~/.tailpipe/data/default/tailpipe.db
$ duckdb tailpipe.db
D SELECT 
    tp_date,
    tp_index as server,
    count(*) as requests
  FROM nginx_access_log 
  WHERE tp_date = '2024-11-01'
  GROUP BY tp_date, tp_index;
```

This flexibility means you can:
- Use your favorite DuckDB client to analyze the data
- Write scripts that process the data directly
- Import the data into other tools that support parquet files
- Build automated reporting systems around the collected data

## Join with External Data

One of Tailpipe's powerful features is the ability to join log data with other tables. Here's an example joining with an IP information table to get more context about the traffic:

```sql
SELECT
    n.remote_addr as ip,
    i.description,
    count(*) as requests,
    count(distinct n.server_name) as servers_accessed,
    round(avg(n.bytes_sent)) as avg_bytes,
    string_agg(distinct n.method, ', ') as methods_used,
    count(CASE WHEN n.status >= 400 THEN 1 END) as errors
FROM nginx_access_log n
LEFT JOIN ip_info i ON n.remote_addr = i.ip_address
WHERE i.description IS NOT NULL
GROUP BY n.remote_addr, i.description
ORDER BY requests DESC;
```

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│ ip          descripti…  requests    servers_a…  avg_bytes   methods_u…  errors       │
│────────────────────────────────────────────────────────────────────────────────────  │
│ 203.0.113…  Test Netw…  1           1           1860        GET         0           │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

This enriched query shows:
- IP addresses and their descriptions
- How many servers each IP accessed
- Average response sizes
- HTTP methods used
- Error counts

## What's Next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Discover more plugins on the Hub →](https://hub.steampipe.io/plugins)
- [Learn about data compaction and optimization →](https://tailpipe.io/docs/managing/compaction)
- [Share data with your team using remotes →](https://tailpipe.io/docs/sharing/remotes)
- [Create schemas for filtered views →](https://tailpipe.io/docs/schemas)
- [Join #tailpipe on Slack →](https://turbot.com/community/join)
