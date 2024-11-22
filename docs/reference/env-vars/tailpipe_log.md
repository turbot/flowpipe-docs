---
title: TAILPIPE_LOG_LEVEL
sidebar_label: TAILPIPE_LOG_LEVEL
---

# TAILPIPE_LOG_LEVEL

Sets the output logging level.  Standard log levels are supported (`DEBUG`, `INFO`, `WARN`, `ERROR`). Logs are written to STDERR.  By default, logging is off.

Logs are written to STDERR.

## Usage 

Turn on info level logging:
```bash
export TAILPIPE_LOG_LEVEL=info
```

Turn off logging:

```bash
unset TAILPIPE_LOG_LEVEL
```

Redirect logs to a file:
```bash
TAILPIPE_LOG_LEVEL=debug tailpipe collect nginx_access_log.dev 2> mylogs.txt
```