---
title: FLOWPIPE_MAX_CONCURRENCY_FUNCTION
sidebar_label: FLOWPIPE_MAX_CONCURRENCY_FUNCTION
---
# FLOWPIPE_MAX_CONCURRENCY_FUNCTION

Sets the maximum number of `function` step instances that can execute concurrently across all pipeline instances.  When the limit is reached, `function` steps will be queued until enough `function` steps complete to bring the count under the limit. 

The minimum value is `1`, and the default is `50`.

## Usage 


Set the concurrency to 100:
```bash
export FLOWPIPE_MAX_CONCURRENCY_FUNCTION=100
```

Reset to the default value:
```bash
unset FLOWPIPE_MAX_CONCURRENCY_FUNCTION
```
