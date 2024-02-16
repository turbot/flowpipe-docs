---
title:  Webform Integration
sidebar_label: webform
---

# Webform Integration

The Webform integration provides a mechanism for responding to inputs by visiting a web page and submitting a form.  This integration requires no arguments and will be created by default.

```hcl
integration "webform" "default" {}
```

By default,  Flowpipe will include the default `webform` integration in the [default `notifier`](#default-notifier) so that input steps can work out of the box.

## Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `title`         | String    | Optional    | Display title for the integration.
