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
- Create filtered views of your data using schemas
- Join log data with other data sources for enriched analysis

## Install the NGINX Plugin

This tutorial uses the NGINX plugin to demonstrate collecting and analyzing web server access logs. First, [download and install Tailpipe](/downloads), and then install the plugin:

```bash
tailpipe plugin install nginx
```

## Configure Data Collection

Tailpipe uses HCL configuration files to define what data to collect. Create a file named `nginx.tpc` with the following content.

```hcl
partition "nginx_access_log" "dev" {
    plugin = "nginx"
    source "file_system"  {
        paths = ["/path/to/dev1.log", "/path/to/dev2.log"]
        extensions = [".log"]    
    }
}
```

This configuration tells Tailpipe to collect NGINX access logs from two log files. The configuration defines:

- A table named `nginx_access_log`. This is the table your SQL queries will target.

- A partition named `dev`. This is a logical segment within the table, we'll start with just one partition.

- The source type "nginx_access_log_file" which reads NGINX formatted logs

- The paths to the partition's log files.


## Collect Data

Now let's collect the logs:

```bash
tailpipe plugin install nginx
```

```bash
tailpipe collect nginx_access_log.dev
```

```bash
Collection complete
artifacts discovered: 1, artifacts downloaded: 1, artifacts extracted: 1, rows enriched: 13748, rows converted: 13748, errors: 0
Execution 1731389149496 complete
Timing (may overlap):
 - discover:   90.949µs
 - download:   109.872µs (active: 7.902µs)
 - extract:    367.273936ms (active: 367.251937ms)
 - enrich:     367.155607ms (active: 129.880729ms)
 - convert:    5.368437944s (active: 3.06050278s)
 - total time: 5.403065682s
 ```

 This command:

- Read the NGINX access logs from the specified files

- Parsed and standardized the log entries

- Stored the data in parquet files organized by date

- Updated the local database with SQL views over the parquet files


## Query Your Data

Tailpipe provides an interactive SQL shell (or you can query directly with DuckDB). Let's count the log entries we just collected.

./tailpipe query "select count(*) as lines from nginx_access_log"

```bash
┌──────────────┐
│ lines        │
│────────────  │
│  23748       │
└──────────────┘
```

nLet's see the table's schema.


```bash
D select name, type from pragma_table_info('nginx_access_log') order by name;
```

```
+---------------------+-----------+
|        name         |   type    |
+---------------------+-----------+
| body_bytes_sent     | BIGINT    |
| http_referer        | VARCHAR   |
| http_user_agent     | VARCHAR   |
| http_version        | VARCHAR   |
| method              | VARCHAR   |
| path                | VARCHAR   |
| remote_addr         | VARCHAR   |
| remote_user         | VARCHAR   |
| request             | VARCHAR   |
| status              | BIGINT    |
| time_iso_8601       | VARCHAR   |
| time_local          | VARCHAR   |
| timestamp           | TIMESTAMP |
| tp_akas             | VARCHAR[] |
| tp_date             | DATE      |
| tp_destination_ip   | VARCHAR   |
| tp_domains          | VARCHAR[] |
| tp_emails           | VARCHAR[] |
| tp_id               | VARCHAR   |
| tp_index            | VARCHAR   |
| tp_ingest_timestamp | TIMESTAMP |
| tp_ips              | VARCHAR[] |
| tp_partition        | VARCHAR   |
| tp_source_ip        | VARCHAR   |
| tp_source_location  | VARCHAR   |
| tp_source_name      | VARCHAR   |
| tp_source_type      | VARCHAR   |
| tp_tags             | VARCHAR[] |
| tp_timestamp        | TIMESTAMP |
| tp_usernames        | VARCHAR[] |
+---------------------+-----------+
```

Some of the columns correspond to the fields in a raw nginx log: `http_user_agent`, `remote_addr`, etc.

Others map the raw fields to [Tailpipe common fields]().

```
type CommonFields struct {
	TpID              string    `json:"tp_id"`
	TpSourceType      string    `json:"tp_source_type"`
	TpSourceName      string    `json:"tp_source_name"`
	TpSourceLocation  *string   `json:"tp_source_location"`
	TpIngestTimestamp time.Time `json:"tp_ingest_timestamp"`

	// Standardized
	TpTimestamp     time.Time `json:"tp_timestamp"`
	TpSourceIP      *string   `json:"tp_source_ip"`
	TpDestinationIP *string   `json:"tp_destination_ip"`

	// Hive fields
	TpPartition string `json:"tp_partition"`
	TpIndex     string `json:"tp_index"`
	TpDate      string `json:"tp_date"`

	// Searchable
	TpAkas      []string `json:"tp_akas,omitempty"`
	TpIps       []string `json:"tp_ips,omitempty"`
	TpTags      []string `json:"tp_tags,omitempty"`
	TpDomains   []string `json:"tp_domains,omitempty"`
	TpEmails    []string `json:"tp_emails,omitempty"`
	TpUsernames []string `json:"tp_usernames,omitempty"`
}
```

Still others synthesize columns from raw fields, like `http_version` and `method`. We see all these ingredients in each row created by the nginx plugin.

```
D select * from nginx_access_log limit 1;
```

```
            request = GET /login HTTP/1.1
       http_referer = -
  tp_destination_ip =
     tp_source_type = file_system
        remote_user = -
tp_ingest_timestamp = 2024-11-12 10:22:01.430331
      time_iso_8601 = 2024-11-01T00:00:00Z
       http_version = 1.1
           tp_index = dev1.log
            tp_tags = [method:GET]
       tp_partition = dev
            tp_akas = [/login]
             method = GET
          timestamp = 2024-11-01 00:00:00
          tp_emails =
        remote_addr = 192.29.251.248
               path = /login
             status = 200
    body_bytes_sent = 12471
            tp_date = 2024-11-01
       tp_usernames =
              tp_id = cspioiann1tkcge0uslg
    http_user_agent = Mozilla/5.0 (Linux; Android 10; K) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Mobile Safari/537.36
     tp_source_name =
       tp_source_ip = 192.29.251.248
         tp_domains =
             tp_ips = [192.29.251.248]
         time_local = 01/Nov/2024:00:00:00 +0000
 tp_source_location = /home/jon/tpsrc/tailpipe-plugin-nginx/test/dev1.log
```

### Analyze HTTP methods

Let's look at the distribution of HTTP methods in the log.

```sql
select 
  substr(method, 0, 10) as method, 
  count(*) as count 
from 
  nginx_access_log 
group by method 
  order by count desc;
```

```
+-----------+-------+
|  method   | count |
+-----------+-------+
| GET       | 23473 |
| POST      | 195   |
| PUT       | 25    |
| DELETE    | 23    |
|           | 14    |
| HEAD      | 14    |
| PRI       | 2     |
| \x16\x03\ | 1     |
| \x16\x03\ | 1     |
+-----------+-------+
```

There are some anomalies, let's explore them.

```sql
select
    substr(method, 0, 10) as method,
    tp_timestamp,
    tp_source_ip,
    path,
    status,
    http_user_agent
  from
    nginx_access_log
  where
    method not in ('GET', 'POST', 'PUT', 'DELETE', 'HEAD', 'PATCH')
    and method is not null
    and method != ''
  order by
    tp_timestamp desc;
```

```
+-----------+---------------------+----------------+----------------------------------------------------------------------------+--------+-----------------+
|  method   |    tp_timestamp     |  tp_source_ip  |                                    path                                    | status | http_user_agent |
+-----------+---------------------+----------------+----------------------------------------------------------------------------+--------+-----------------+
| PRI       | 2024-11-02 21:06:37 | 167.94.138.50  | *                                                                          | 400    | -               |
| \x16\x03\ | 2024-11-02 08:29:14 | 198.44.237.38  | W\xD2\x9B\x7F\xD0\xDC\xD2\xED\xD3\x11\x9A\xF1\xE6\xDA\xF5\x8C\xF5\x13#\x14 | 400    | -               |
| \x16\x03\ | 2024-11-02 04:25:25 | 198.235.24.174 | \x1E\xF6                                                                   | 400    | -               |
| PRI       | 2024-11-02 01:18:28 | 167.94.145.109 | *                                                                          | 400    | -               |
+-----------+---------------------+----------------+----------------------------------------------------------------------------+--------+-----------------+
```

These results suggest automated probes looking for potential vulnerabilities, particularly around HTTP/2 prefaces or raw SSL/TLS connections on HTTP ports.


## Understanding Data Storage

Tailpipe uses a hive-partitioned storage structure that organizes data for efficient querying. Let's look at how data is stored for the configuration we've explored so far.

```
├── nginx_access_log
│  └── tp_partition=dev
│      ├── tp_index=dev1.log
│      │  ├── tp_date=2024-11-01
│      │  │  ├── file_487ad4ad-b6cd-4333-babd-a81984b2e895.parquet
│      │  │  └── file_ee578641-0436-4fc5-8609-fbfbb184a139.parquet
│      │  └── tp_date=2024-11-02
│      │      ├── file_064c25c8-20cc-4e46-9c72-1e4058f3f507.parquet
│      │      ├── file_34ae2e7c-e9c2-41c6-baea-61b825b518da.parquet
│      │      ├── file_40e70129-37e2-406b-a188-f171e8ff5d9a.parquet
│      └── tp_index=dev2.log
│          ├── tp_date=2024-11-01
│          │  ├── file_08eb2b06-10c7-421d-bf80-accf6af49f4c.parquet
│          │  ├── file_1750ac16-23f1-40f8-bbf1-fe4fa7652d1d.parquet
│          └── tp_date=2024-11-02
│              ├── file_1f0af7e0-c60b-4ccb-9369-a8996777e06e.parquet
│              ├── file_2ac1c10f-efbf-4ba6-aaa3-12f95b9e33dc.parquet
```

The structure has several key components:

- **Partition**: Segments the data by a top-level key. Here we've defined a `dev` partition with logs for two servers, `dev1` and `dev2`. 

- **Index**: Sub-divides data by a secondary key. Here we index on the servers `dev1` and `dev2`.

- **Date**: Further partitions data by date. 

This hierarchical structure enables efficient querying by means of partition pruning. When you query with conditions on `tp_partition`, `tp_index`, or `tp_date`, Tailpipe can skip reading irrelevant parquet files entirely.

If you want to analyze logs from production servers as well as dev servers, just define another partition!

```hcl
partition "nginx_access_log" "dev" {
    plugin = "nginx"
    source "file_system"  {
        paths = ["/path/to/dev1.log", "/path/to/dev2.log"]
        extensions = [".log"]    
    }
}

partition "nginx_access_log" "prod" {
    plugin = "nginx"
    source "file_system"  {
        paths = ["/path/to/prod1.log", "/path/to/prod2.log"]
        extensions = [".log"]    
    }
}
```


### Querying with DuckDB 

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

```
+------------+----------+----------+
|  tp_date   |  server  | requests |
+------------+----------+----------+
| 2024-11-01 | dev1.log | 1000     |
| 2024-11-01 | dev2.log | 5423     |
+------------+----------+----------+
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
FROM nginx_access_log n
LEFT JOIN ip_info i ON n.remote_addr = i.ip_address
WHERE i.description IS NOT NULL
GROUP BY n.remote_addr, i.description
ORDER BY requests DESC;
```
The query joins nginx data with a table of IP addresses to enrich them with descriptions.


## What's Next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Discover more plugins on the Hub →](https://hub.steampipe.io/plugins)

- [Learn about data compaction and optimization →](https://tailpipe.io/docs/managing/compaction)

- [Create schemas for filtered views →](https://tailpipe.io/docs/schemas)

