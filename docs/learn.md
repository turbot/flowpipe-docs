---
id: learn
title: Learn Tailpipe
sidebar_label: Learn Tailpipe
slug: /
---

[Graza version](#learn-tailpipe-graza) | [Jon version](#learn-tailpipe-jon)

# Learn Tailpipe Graza

Tailpipe allows you to create "pipelines as code" to define workflows and other tasks that run in a sequence.

## Creating your first pipeline

Getting started is easy! If you haven't already done so, [download and install Tailpipe](/downloads).

Tailpipe pipelines and triggers are packaged into [mods](/docs/build), and Tailpipe requires a mod to run. Let's create a new directory for our mod, and then run `tailpipe mod init` to initialize it:

```bash
mkdir learn_tailpipe
cd learn_tailpipe
tailpipe mod init
```

The `tailpipe mod init` command creates a file named `mod.fp` in the directory. This file contains a `mod` definition for our new mod:

```hcl
mod "local" {
  title = "learn_tailpipe"
}
```

You can customize the [mod definition](/docs/tailpipe-hcl/mod) if you like, but the default is sufficient for our purposes.

Let's create our first pipeline.

Tailpipe mods are written in HCL. When Tailpipe runs, it will load the mod from the working directory and will read all files with the `.fp` extension from the directory and its subdirectories recursively. Create a file named `learn.fp` and add the following code:

```hcl
pipeline "learn_tailpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }
}
```

A Tailpipe [pipeline](/docs/tailpipe-hcl/step/pipeline) is a sequence of steps to do work. This snippet creates a pipeline called `learn_tailpipe` that has a single [http step](/docs/tailpipe-hcl/step/http), and a single [output](/docs/tailpipe-hcl/step/pipeline#outputs).

Let's run it!

```bash
tailpipe pipeline run learn_tailpipe
```

![](/images/docs/learn/get-ipv4.png)

Tailpipe runs the pipeline and prints its outputs once it is complete.

When troubleshooting, it's often useful to view more information about the currently executing steps.  You can use the `--verbose` flag to show this detailed information.

```bash
tailpipe pipeline run learn_tailpipe --verbose
```

![](/images/docs/learn/get-ipv4-verbose.png)

## Using mods

Tailpipe's modular design allows you to build pipelines from other pipelines. Let's install the `reallyfreegeoip` mod:

```bash
tailpipe mod install github.com/turbot/tailpipe-mod-reallyfreegeoip
```

```bash
Installed 1 mod:

local
└── github.com/turbot/tailpipe-mod-reallyfreegeoip@v0.1.0
```

The mod is installed into the `.tailpipe/mods` subdirectory, and a dependency is added to your `mod.fp`.

Now that the mod is installed, you should see its pipelines:

```bash
tailpipe pipeline list
```

```bash
MOD                    NAME                                           DESCRIPTION
local                  learn_tailpipe
reallyfreegeoip        reallyfreegeoip.pipeline.get_ip_geolocation    Get geolocation data for an IPv4 or IPv6 address.
```

You can run pipelines from the dependency mod on the command line:

```bash
tailpipe pipeline run reallyfreegeoip.pipeline.get_ip_geolocation --arg ip_address=35.236.238.30
```

![](/images/docs/learn/reallyfreegeoip.png)

## Composing with pipelines

While running the dependency pipelines directly in the CLI is useful, the real power is the ability to compose pipelines from other pipelines. Let's add a [pipeline step](/docs/tailpipe-hcl/step/pipeline) to take our IP address and look up our geo-location information.

```hcl
pipeline "learn_tailpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  step "pipeline" "get_geo" {
    pipeline = reallyfreegeoip.pipeline.get_ip_geolocation
    args = {
      ip_address = step.http.get_ipv4.response_body.ip
    }
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }

  output "latitude" {
    value = step.pipeline.get_geo.output.geolocation.latitude
  }

  output "longitude" {
    value = step.pipeline.get_geo.output.geolocation.longitude
  }
}
```

Notice that we used the IP address from the first step (`step.http.get_ipv4.response_body.ip`) as an argument to the second step. Tailpipe automatically detects this dependency and runs the steps in the correct order!

Let's add a couple more steps to get the weather forecast for our location.

```hcl
pipeline "learn_tailpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  step "pipeline" "get_geo" {
    pipeline = reallyfreegeoip.pipeline.get_ip_geolocation

    args = {
      ip_address = step.http.get_ipv4.response_body.ip
    }
  }

  step "http" "get_weather" {
    url = join("", [
      "https://api.open-meteo.com/v1/forecast",
      "?latitude=${step.pipeline.get_geo.output.geolocation.latitude}",
      "&longitude=${step.pipeline.get_geo.output.geolocation.longitude}",
      "&current=temperature",
      "&forecast_days=1",
      "&daily=temperature_2m_min,temperature_2m_max,precipitation_probability_mean",
      "&temperature_unit=${step.pipeline.get_geo.output.geolocation.country_code == "US" ? "fahrenheit" : "celsius"}"
    ])
  }

  step "transform" "friendly_forecast" {
    value = join("", [
      "It is currently ",
      step.http.get_weather.response_body.current.temperature,
      step.http.get_weather.response_body.current_units.temperature,
      ", with a high of ",
      step.http.get_weather.response_body.daily.temperature_2m_max[0],
      step.http.get_weather.response_body.daily_units.temperature_2m_max,
      " and a low of ",
      step.http.get_weather.response_body.daily.temperature_2m_min[0],
      step.http.get_weather.response_body.daily_units.temperature_2m_min,
      ".  There is a ",
      step.http.get_weather.response_body.daily.precipitation_probability_mean[0],
      step.http.get_weather.response_body.daily_units.precipitation_probability_mean,
      " chance of precipitation."
    ])
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }

  output "latitude" {
    value = step.pipeline.get_geo.output.geolocation.latitude
  }

  output "longitude" {
    value = step.pipeline.get_geo.output.geolocation.longitude
  }

  output "forecast" {
    value = step.transform.friendly_forecast.value
  }
}
```

![](/images/docs/learn/weather-report.png)



## Send a message

Now we have a pipeline that can get the local forecast - let's send it somewhere!  The [message step](/docs/tailpipe-hcl/step/message) provides a mechanism for sending messages via multiple communication channels, such as Slack and Email. 

Add this step to the `learn_tailpipe` pipeline.

```hcl
  step "message" "send_forecast" {
    notifier = notifier.default
    subject  = "Todays Forecast"
    text     = step.transform.friendly_forecast.value
  }
```

And run the pipeline again.

```bash
tailpipe pipeline run learn_tailpipe
```

You should see the message printed to the console when you run the pipeline. 

Console messages and inputs are useful, but Tailpipe can also route these input requests, approvals and notifications to external systems like Slack, MS Teams, and Email!

Tailpipe [Integrations](/docs/reference/config-files/integration) allow you to interface with external systems.  [Notifiers](/docs/reference/config-files/notifier) allow you to route [message](/docs/tailpipe-hcl/step/message) and [input](/docs/build/input) steps to one or more integrations.  Integrations are only loaded in [server-mode](/docs/run/server).

 
Tailpipe server creates a default [`http` integration](/docs/reference/config-files/integration/http) as well as a [default notifier](/docs/reference/config-files/notifier#default-notifier) that routes to it, but you can send it via [Email](/docs/reference/config-files/integration/email), [Slack](/docs/reference/config-files/integration/slack) or [Microsoft Teams](/docs/reference/config-files/integration/msteams) without modifying the pipeline code.  Just create the appropriate [integrations](/docs/reference/config-files/integration), add them to the [default notifier](/docs/reference/config-files/notifier#default-notifier), and run the pipeline again from a server instance!

```bash
tailpipe server &
tailpipe pipeline run learn_tailpipe --host local
```

![](/images/docs/learn/slack-weather-report.png)


## Learn Tailpipe Jon

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe, you can-

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
tp_table=nginx_access_log
└── tp_partition=dev
    ├── tp_index=dev1
    │   ├── tp_date=2024-11-12
    │   │   ├── file_2b8c5008-09d6-4ada-9065-32263a4f5539.parquet
    │   │   ├── file_4ccaaed5-0c2b-46c6-9c07-75cd4d086051.parquet
    │   │   ├── file_6252d495-dae3-43f1-a77f-14d1408af2f8.parquet
    │   │   ├── file_c50479b6-0684-43a5-917a-95b1e003358e.parquet
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

# Learn Tailpipe Jon

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe, you can:

- Collect logs from various sources and store them efficiently in parquet files
- Query your data using familiar SQL syntax through DuckDB
- Create filtered views of your data using schemas
- Join log data with other data sources for enriched analysis

## Install the nginx Plugin

This tutorial uses the nginx plugin to demonstrate collecting and analyzing web server access logs. First, [download and install Tailpipe](/downloads), and then install the plugin:

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

This configuration tells Tailpipe to collect nginx access logs from two log files. The configuration defines:

- A table named `nginx_access_log`. This is the table your SQL queries will target.

- A partition named `dev`. This is a logical segment within the table, we'll start with just one partition.

- The source type `nginx_access_log_file` which reads nginx logs

- The paths to the partition's log files.

## Collect Data

Now let's collect the logs:

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

- Read the nginx access logs from the specified files

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

Let's see the table's schema.


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

Others map the raw fields to [Tailpipe common fields]() like `TpTimestamp` and `TpIps`.

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

### Query across server logs

Now that we've collected from both server logs, we can query across them.

```sql
tailpipe query "SELECT tp_date, tp_index as server, count(*) as requests
    FROM nginx_access_log
    WHERE tp_date = '2024-11-01'
    GROUP BY tp_date, tp_index;"
```

```
┌──────────────────────────────────────┐
│ tp_date      server      requests    │
│______________________________________│
│  2024-11-01  dev1.log    1000        │
│  2024-11-01  dev2.log    5423        │
└──────────────────────────────────────┘
```

> [!NOTE]
> Tailpipe stores data in a DuckDB database that you can query directly.

## Join Across the Tailpipe Ecosystem and Beyond

Tailpipe's common fields enable joins across tables created by different Tailpipe plugins.

You can also populate the Tailpipe database with other tables and join those with Tailpipe tables.

Here's an example that joins the `nginx_access_log` table with an IP information table to enrich the IP addresses found in the logs.

```sql
SELECT
    n.remote_addr as ip,
    i.description,
    count(*) as requests,
FROM nginx_access_log n  -- tailpipe table
LEFT JOIN ip_info i      -- non-tailpipe table
ON n.remote_addr = i.ip_address
WHERE i.description IS NOT NULL
GROUP BY n.remote_addr, i.description
ORDER BY requests DESC;
```
The query joins nginx data with a table of IP addresses to enrich them with descriptions.

## What's Next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Discover more plugins on the Hub →](https://hub.tailpipe.io/plugins)

- [Learn about data compaction and optimization →](https://tailpipe.io/docs/managing/compaction)

- [Create schemas for filtered views →](https://tailpipe.io/docs/schemas)