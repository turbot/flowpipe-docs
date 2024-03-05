---
title:  Email Integration
sidebar_label: email
---

# Email Integration

Use the `email` integration to send email messages with a [`message` step](/docs/flowpipe-hcl/step/message) or to prompt for user input in an [`input` step](/docs/flowpipe-hcl/step/input).

```hcl
integration "email" "my_email_app" {
  smtp_host     = "smtp.gmail.com"
  from          =  "flowpipe@bluth.com"
  smtp_username = "flowpipe@bluth.com"
  smtp_password = "Shfakes87y98324509i"  
}
```


To prompt for input via email in an `input` step, you must create an email `integration`, add it to a [notifier](/docs/reference/config-files/notifier), and then configure your [`input` step](/docs/flowpipe-hcl/step/input) to send to the notifier. 

For `input` steps to work properly with your email integration, make sure you set your [Base URL](/docs/reference/config-files/integration#base-url) to an address that the recipient can route to.  You can set the base URL via the [FLOWPIPE_BASE_URL](/docs/reference/env-vars/flowpipe_base_url) environment variable, the `base_url` [workspace argument](/docs/reference/config-files/workspace), or pass it via the `--base-url` argument to the [flowpipe server](/docs/reference/cli/server) command.

## Arguments


| Argument        | Type      | Optional?   | Description
|-----------------|-----------|-------------|-----------------
| `from`          | String    | Required	  | The email address from which to send messages.  This value should be considered a default; if a `notifier` or an `input` step specifies a `from` it will override this value.
| `smtp_host`     | String   | Required	    | The SMTP host to send messages to.
| `bcc`            | List&ltString&gt | Optional	  | The email recipients to blind carbon copy (BCC) messages to.  This value should be considered a default; if a `notifier` or an `input` step specifies a `bcc` it will override this value.
| `cc`            | List&ltString&gt | Optional	  | The email recipients to carbon copy (CC) messages to.  This value should be considered a default; if a `notifier` or an `input` step specifies a `cc` it will override this value.
| `smtp_port`     | String   | Optional     | The TCP port to connect to when sending messages via SMTP.  Defaults to `25`.
| `smtp_password` | String   | Optional     | Password when using authenticated SMTP/SMTPS.
| `smtp_tls`      | String   | Optional     | TLS negotiation options.  Values are `required` (only use SMTPS/TLS), `off` (only use SMTP), or `auto` (prefer SMTPS, fallback to SMTP). The default is `auto`.
| `smtp_username` | String   | Optional     | Username when using authenticated SMTP/SMTPS.
| `smtps_port`    | String   | Optional     | The TCP port to connect to when sending messages via SMTPS.  Defaults to `587`.
| `subject`       | String   | Optional	    | The email subject.  This value should be considered a default; if a `notifier` or an `input` step specifies a `subject` it will override this value.
| `to`            | List&ltString&gt | Optional	  | The email recipients to send messages to.  This value should be considered a default; if a `notifier` or an `input` step specifies a `to` it will override this value.
| `title`         | String    | Optional    | Display title for the integration.


## Attributes (Read-Only)

| Attribute       | Type    |  Description
|-----------------|---------|-----------------
| `request_url`   | String  | The URL to where email responses will be sent.
