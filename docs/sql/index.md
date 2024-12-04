---
title: It's Just SQL!
sidebar_label: It's Just SQL!
---

# It's Just SQL!

Tailpipe leverages DuckDB to provide a SQL interface to your Nginx access logs. You can use standard SQL syntax to analyze your web server traffic data.

## Basic SQL

Like most popular databases, DuckDB supports standard SQL syntax - If you know SQL, you already know how to query your logs!

You can **query all the columns** in a table:
```sql
select * from nginx_access_log;
```

You can **filter** rows where columns only have a specific value: 
```sql
select
  remote_addr,
  method,
  path,
  status
from
  nginx_access_log
where
  method = 'GET';
```

or a **range** of values:
```sql
select
  remote_addr,
  method,
  path,
  status
from
  nginx_access_log
where
  status in (404, 500);
```

or match a **pattern**: 
```sql
select
  remote_addr,
  method,
  path,
  status
from
  nginx_access_log
where
  path like '/api/%';
```

You can **filter on multiple columns**, joined by `and` or `or`:
```sql
select
  remote_addr,
  method,
  path,
  status
from
  nginx_access_log
where
  method = 'POST'
  and status >= 400;
```

You can **sort** your results:
```sql
select
  remote_addr,
  body_bytes_sent,
  timestamp
from
  nginx_access_log
order by
  body_bytes_sent;
```

You can **sort on multiple columns, ascending or descending**:
```sql
select
  remote_addr,
  body_bytes_sent,
  timestamp
from
  nginx_access_log
order by
  body_bytes_sent desc,
  timestamp asc;
```

You can group and use standard aggregate functions. You can **count** results:
```sql
select
  method,
  count(*) as count
from
  nginx_access_log
group by
  method
order by
  count desc;
```

or **sum** them:
```sql
select
  path,
  sum(body_bytes_sent) as total_bytes
from
  nginx_access_log
group by
  path;
```

or find **min**, **max**, and **average**:
```sql
select
  path,
  min(body_bytes_sent) as min_bytes,
  max(body_bytes_sent) as max_bytes,
  avg(body_bytes_sent) as avg_bytes
from
  nginx_access_log
group by
  path;
```

You can **exclude duplicate rows**:
```sql
select distinct
  remote_addr
from
  nginx_access_log;
```

or exclude **all but one matching row**:
```sql
select distinct on (remote_addr)
  remote_addr,
  timestamp,
  request
from
  nginx_access_log;
```

