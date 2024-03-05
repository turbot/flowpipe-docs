---
title: FLOWPIPE_MAX_CONCURRENCY_QUERY
sidebar_label: FLOWPIPE_MAX_CONCURRENCY_QUERY
---
# FLOWPIPE_MAX_CONCURRENCY_QUERY

Sets the maximum number of `query` step instances that can execute concurrently across all pipeline instances.  When the limit is reached, `query` steps will be queued until enough `query` steps complete to bring the count under the limit. 

The minimum value is `1`, and the default is `50`.

## Usage 


Set the concurrency to 100:
```bash
export FLOWPIPE_MAX_CONCURRENCY_QUERY=100
```

Reset to the default value:
```bash
unset FLOWPIPE_MAX_CONCURRENCY_QUERY
```
