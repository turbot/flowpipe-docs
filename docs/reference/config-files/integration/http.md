---
title:  HTTP Integration
sidebar_label: http
---

# HTTP Integration

The `http` integration provides a mechanism for responding to inputs by visiting a web page and submitting a form.  This integration requires no arguments and will be created by default.  Currently, when sending a message to an `http` integration from a [message step](/docs/flowpipe-hcl/step/message), Flowpipe prints the message to the console.

```hcl
integration "http" "default" {}
```

By default,  Flowpipe will include the default `http` integration in the [default `notifier`](#default-notifier) so that input steps can work out of the box.

## Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `title`         | String    | Optional    | Display title for the integration.
