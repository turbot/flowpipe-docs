---
title: FLOWPIPE_BASE_URL
sidebar_label: FLOWPIPE_BASE_URL
---

# FLOWPIPE_BASE_URL

## Usage 

Sets the base URL to use for [triggers](/docs/flowpipe-hcl/trigger) and [integrations](/docs/reference/config-files/integration).  This is the base URL that Flowpipe advertises when it interacts with external systems to allow them to call back to Flowpipe.  

The base URL defaults `localhost` on the [Flowpipe port](/docs/reference/env-vars/flowpipe_port), eg `http://localhost:7103`.  This is sufficient for local testing of `http` triggers and `webform` inputs, but to allow external users and systems to interact with Flowpipe, you should set `FLOWPIPE_BASE_URL` to a URL that will route to your system.  This may include the DNS or IP address of the system on which you are running Flowpipe, or it may be a reverse proxy such as [ngrok](https://ngrok.com/).


## Usage

blah
```bash
export FLOWPIPE_BASE_URL=https://84c5df474.ngrok-free.dev
```

Reset the base URL to the default:
```bash
unset FLOWPIPE_BASE_URL
```