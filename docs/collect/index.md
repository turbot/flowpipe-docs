---
title: Collect Logs
sidebar_label: Collect Logs
---

# Collecting logs

You run collection on the command line.

You can collect everything:

```bash
tailpipe collect
```

or table:

```bash
tailpipe collect aws_cloudtrail_log
```

or just a partition:

```bash
tailpipe collect aws_cloudtrail_log.prod
```

The collection process always writes to a local **workspace**, and does so on a per-partition basis.  While the user may specify multiple partitions on the command line, `partition` is the unit of collection.  A partition day is the atomic unit of work; the partition collection succeeds or fails for all sources for a given day.

When a partition is collected, each source resumes from the last time it was collected.  Source data is ingested, standardized, then written to parquet files in the [standard hive structure](tbd).  In addition, [metadata manifest](tbd) data is written for the collection run. 

