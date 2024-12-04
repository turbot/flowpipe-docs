---
title: Querying IP Addresses
sidebar_label: Querying IP Addresses
---

# Querying IP Addresses

One of the primary uses of analyzing Nginx logs is understanding your web traffic patterns and identifying potential security issues. The `remote_addr` field in Nginx logs contains IP addresses of clients accessing your web server.

You can **find traffic from specific IP addresses**:
```sql
select
  remote_addr,
  method,
  path,
  status,
  timestamp
from
  nginx_access_log
where
  remote_addr = '192.168.1.100';
```

You can find requests from IPs that are **within a specific network range**:
```sql
select
  remote_addr,
  count(*) as request_count,
  sum(body_bytes_sent) as total_bytes
from
  nginx_access_log
where
  remote_addr <<= '10.0.0.0/8'
group by
  remote_addr
order by
  request_count desc;
```

You can find traffic that's **not from internal networks**:
```sql
select
  remote_addr,
  count(*) as request_count
from
  nginx_access_log
where
  not (remote_addr::inet <<= '10.0.0.0/8'::inet)
  and not (remote_addr::inet <<= '192.168.0.0/16'::inet)
  and not (remote_addr::inet <<= '172.16.0.0/12'::inet)
group by
  remote_addr
order by
  request_count desc;
```

