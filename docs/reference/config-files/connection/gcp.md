---
title: gcp
sidebar_label: gcp
---

# gcp

The `gcp` connection can be used to access Google Cloud Platform resources.

```hcl
connection "gcp" "gcp_def" {
  connections = "~/.config/gcloud/my_connections.json"
}
```

## Arguments

| Name          | Type   | Required? | Description                                                                                                                                          |
| ------------- | ------ | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `connections` | String | Optional  | Either the path to a JSON connection file that contains Google application connections or the contents of a service account key file in JSON format. |
| `ttl`         | Number | Optional  | The time, in seconds, to cache the connections. By default, the GP connection will be cached for 5 minutes.                                          |

All arguments are optional, and a `gcp` connection with no arguments will behave the same as the [GCP default connection](#default-connection).

## Attributes (Read-Only)

| Attribute      | Type   | Description                                          |
| -------------- | ------ | ---------------------------------------------------- |
| `access_token` | String | An OAuth access token to use to authenticate to GCP. |

## Default Connection

The GCP connection type includes an implicit, default connection (`connection.gcp.default`) that will be configured using the same mechanism as the GCloud CLI (environment variables, config files, etc); the effective connections of this default are the same as if you run the `gcloud` command. Connections will be loaded from:

- The path specified in the `GOOGLE_APPLICATION_CONNECTIONS` environment variable, if set; otherwise
- The standard location (`~/.config/gcloud/application_default_connections.json`)

## Examples

### Static Connections from Connection File

```hcl
connection "gcp" "gcp_def" {
  connections = "~/.config/gcloud/my_connections.json"
}
```

### Static Connections defined inline

```hcl
connection "gcp_inline" {
  #project = "my_project"
  connections = <<EOC
  {
  "client_id": "blah-blah.apps.googleusercontent.com",
  "client_secret": "d-blah",
  "quota_project_id": "dev-aaa",
  "refresh_token": "1//blah-blah",
  "type": "authorized_user"
  }
  EOC
}
```

### Using GCP Connections in Container Step

<!-- NOTE: Waiting on confirmation -->

```hcl
pipeline "gcp_test" {
    param "cred" {
    type    = string
    default = "default"
  }

  step "container" "get_instances" {
    image = "gcr.io/google.com/cloudsdktool/google-cloud-cli"
    cmd   = ["gcloud", "compute", "instances", "list"]
    env = {
      CLOUDSDK_CORE_PROJECT      = "my-project"
      CLOUDSDK_AUTH_ACCESS_TOKEN = connections.gcp[param.cred].access_token
    }
  }
}
```

### Using GCP Connections in HTTP Step

<!-- NOTE: Waiting on confirmation -->

```hcl
pipeline "gcp_test" {
  param "cred" {
    type    = string
    default = "default"
  }

  step "http" "get_instances" {
    url = "https://compute.googleapis.com/compute/v1/projects/my-project/aggregated/instances"

    request_headers = {
      Content-Type = "application/json; charset=utf-8"
      Authorization = "Bearer ${connections.gcp[param.cred].access_token}"
    }
  }
}
```
