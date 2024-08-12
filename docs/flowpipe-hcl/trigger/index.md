---
title: trigger
sidebar_label: trigger
---


# trigger

Triggers are used to execute a pipeline when an event occurs. They are defined in the mod and are based on a schedule, webhook, or other event.

```hcl
trigger "http" "my_webhook" {
  pipeline = pipeline.my_pipeline
  args     = {
    event = self.request_body
  }                              
}
```


## Trigger Types

| Type            | Description
|-------------------|----------------
| [http](/docs/flowpipe-hcl/trigger/http)        | Create a webhook and initiate a pipeline when a request is made to the webhook.
| [query](/docs/flowpipe-hcl/trigger/query)       | Runs a query on schedule or at regular intervals and executes pipelines when there are changes to the result set.
| [schedule](/docs/flowpipe-hcl/trigger/schedule)| Runs a pipeline on schedule or at regular intervals.
