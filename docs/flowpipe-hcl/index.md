---
title: Flowpipe HCL
sidebar_label: Flowpipe HCL
---

# Flowpipe HCL

## General

| Type | Description
|-|-
| [locals](/docs/flowpipe-hcl/locals)         | Locals are internal, module level variables.
| [mod](/docs/flowpipe-hcl/mod)               | The mod block contains metadata, documentation, and dependency data for the mod.
| [pipeline](/docs/flowpipe-hcl/pipeline)     | A pipeline is a set of steps that do work.
| [trigger](/docs/flowpipe-hcl/trigger) | Triggers are used to execute a pipeline when an event occurs.
| [variable](/docs/flowpipe-hcl/variable)     | Variables are module-level objects that essentially act as parameters for a module.


## Step Primitives

| Type            | Description
|-------------------|----------------
| [container](/docs/flowpipe-hcl/step/container)    | Run a Docker container.
| [email](/docs/flowpipe-hcl/step/email)            | Send an email.
| [function](/docs/flowpipe-hcl/step/function)      | Run an AWS Lambda-compatible function.
| [http](/docs/flowpipe-hcl/step/http)              | Make an HTTP request.
| [pipeline](/docs/flowpipe-hcl/step/pipeline) | Run another Flowpipe pipeline.
| [query](/docs/flowpipe-hcl/step/query)            | Run a SQL query.
| [sleep](/docs/flowpipe-hcl/step/sleep)            | Wait for a defined time period.
| [transform](/docs/flowpipe-hcl/step/transform)    | Use HCL functions to transform data .

## Trigger Types
| Type            | Description
|-------------------|----------------
| [http](/docs/flowpipe-hcl/trigger/http)        | Create a webhook and initiate a pipeline when a request is made to the webhook.
| [query](/docs/flowpipe-hcl/trigger/query)       | Runs a query on schedule or at regular intervals and executes pipelines when there are changes to the result set.
| [schedule](/docs/flowpipe-hcl/trigger/schedule)| Runs a pipeline on schedule or at regular intervals.

<!--
| [query](/docs/flowpipe-hcl/trigger/query)      | Run a SQL query on a schedule and pass row changes as an input to the defined pipeline.

-->