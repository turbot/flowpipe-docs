---
title:  Microsoft Teams Integration
sidebar_label: msteams
---

# Microsoft Teams Integration

Use the `msteams` integration to send messages with a [`message` step](/docs/flowpipe-hcl/step/message) or to prompt for user input in an [`input` step](/docs/flowpipe-hcl/step/input).

```hcl
integration "msteams" "general_channel" {
  webhook_url = "https://example-corp.webhook.office.com/webhook2b/..."
}
```

## Arguments

| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `webhook_url `  | String	  | Required	  | A webhook url to post to a Microsoft Teams channel. Note that a webhook is tied to a specific channel; if you specify a channel in the step options it will be ignored.
| `title`         | String    | Optional    | Display title for the integration.

## Examples

### Example: Microsoft Teams integration with webhook URL

> Note: webhook URL is tied to a specific channel; if you specify a channel in the step options it will be ignored.

```hcl
integration "msteams" "general_channel" {
  webhook_url = "https://example.webhook.office.com/webhook2b/aa24647a-xxxx-xxxx-xxxx-89b9xxxx29a0@a475991c-xxxx-xxxx-xxxx-38bbxxxxeb78/IncomingWebhook/92xxxx7ae8xxxx71a0xxxxa9axxxx31e/221809dc-xxxx-xxxx-xxxx-d629xxxx738c"
}
```

## Obtaining a Webhook URL

> Note: Webhook URLs allow unauthenticated messages to be sent to the attached channel and should be treated as secrets.

1. Navigate within Microsoft Teams to the channel you wish to obtain a webhook URL for.
1. Click **...** next to the channel name in the side-menu.
1. Select **Manage Channel** from the drop-down menu.
1. Under **Connectors** click on the **Edit** button.
1. Locate **Incoming Webhook** in the list and click the **Add** button.
1. Provide a name for the webhook (_Optional_ provide an avatar image for the webhook) & click the **Create** button.
1. Copy the generated url as this will be the `webhook_url`
