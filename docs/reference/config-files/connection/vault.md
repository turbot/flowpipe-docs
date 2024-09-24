---
title: vault
sidebar_label: vault
---

# vault

The `vault` connection can be used to access Vault resources.

```hcl
connection "vault" "my_vault" {
  address = "http://127.0.0.1:8200"
  token = "hvs.FaKe"
}
```

## Arguments

| Name      | Type   | Required? | Description          |
| --------- | ------ | --------- | -------------------- |
| `address` | String | Optional  | Vault server address |
| `token`   | String | Optional  | API token            |

All arguments are optional, and a `vault` connection with no arguments will behave the same as the [default connection](#default-connection).

## Attributes (Read-Only)

| Attribute | Type | Description                                                                                  |
| --------- | ---- | -------------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`VAULT_ADDR`, `VAULT_TOKEN`) |

## Default Connection

The `vault` connection type includes an implicit, default connection (`connection.vault.default`) that will be configured to set the `token` to the `VAULT_TOKEN` environment variable and the `domain` to `VAULT_ADDR`.

```hcl
connection "vault" "default" {
  domain = env("VAULT_ADDR")
  token  = env("VAULT_TOKEN")
}
```
