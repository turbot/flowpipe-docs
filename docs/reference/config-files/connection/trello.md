---
title: trello
sidebar_label: trello
---

# trello

The `trello` connection can be used to access Trello resources.

```hcl
connection "trello" "my_trello" {
  api_key = "a25ad2e..."
  token   = "ATTAb179ea..."
}
```

## Arguments

| Name      | Type   | Required? | Description |
| --------- | ------ | --------- | ----------- |
| `api_key` | String | Optional  | API key     |
| `token`   | String | Optional  | API token   |

All arguments are optional, and a `trello` connection with no arguments will behave the same as the [default connection](#default-connection).

## Default Connection

The `trello` connection type includes an implicit, default connection (`connection.trello.default`) that will be configured to set the `api_key` to the `TRELLO_API_KEY` environment variable and the `token` to the `TRELLO_TOKEN` environment variable.

```hcl
connection "trello" "default" {
  api_key = env("TRELLO_API_KEY")
  token   = env("TRELLO_TOKEN")
}
```
