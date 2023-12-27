---
title: Use Triggers
sidebar_label: Use Triggers
---

# Triggers

Triggers are used to execute a pipeline when an event occurs. They are defined in the mod and are based on a schedule, webhook or other event.

Triggers are only instantiated when Flowpipe is running in [server mode](/docs/run/index#operating-modes) - you must start the server with `flowpipe server` for triggers to run.

Only triggers defined in the top-level mod are created, not dependencies.

You can use a `schedule` trigger to run a pipeline at regular intervals:

```
trigger "schedule" "daily_3pm" {
  pipeline = pipeline.learn_flowpipe
  schedule = "* 15 * * *"
}
```

An `http` trigger allows you to run a pipeline whenever someone posts to a webhook.  You can pass the request body or headers to the pipeline.

```hcl
trigger "http" "my_webhook" {
  pipeline = pipeline.learn_flowpipe
  args     = {
    event = self.request_body
  }                              
}
```

Flowpipe will create a webhook endpoint for each `http` trigger.  You can run `flowpipe trigger show` to get the URL.

```bash
flowpipe trigger show local.trigger.http.my_webhook --host local
```
```bash
Name:     local.trigger.http.my_webhook
Pipeline: local.pipeline.learn_flowpipe
Type:     http
Url:      /api/latest/hook/local.trigger.http.my_webhook/93beff14d043e4db69ad28f992e48aa7fb58c17657f6fadfa3f084ba6bbee0a0
```



<!--
Only triggers defined in the top-level mod are created, not dependencies.  You can however "copy" a dependency trigger with `base=` and then override any of its arguments if desired:

```hcl
trigger "query" "aws_unencrypted_ebs_volumes" {
    base = aws.trigger.query.aws_unencrypted_ebs_volumes
    pipeline = pipeline.my_pipeline
    args     = {
      event = self.inserted_rows
    }
}
```

-->