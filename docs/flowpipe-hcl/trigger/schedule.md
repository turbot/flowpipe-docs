---
title: schedule
sidebar_label: schedule
---

# schedule

The `schedule` trigger runs a pipeline on the given schedule. 

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "hourly"

    pipeline = pipeline.twitter_mentions_to_slack
    args = {
        query = "(steampipe OR powerpipe OR flowpipe)"
    }

}
```


## Arguments

| Argument        | Type    | Optional?  | Description
|-----------------|---------|------------|-----------------
| `pipeline`      | Pipeline Reference | Required | A reference to a `pipeline` resource to start when this trigger runs.  
| `schedule`      | String | Required   | [Schedule](#more-examples) to run the query. This may be a named interval (`hourly`, `daily`, `weekly`, `5m`, `10m`, `15m`, `30m`, `60m`, `1h`, `2h`, `4h`, `6h`, `12h`, `24h`) or a custom schedule in cron syntax. 
| `args`	      | Map	   | Optional | A map of arguments to pass to the pipeline.
| `description`   | String | Optional | A string containing a short description of the step. 
| `documentation` | String | Optional | A markdown string containing a long form description, used as documentation for the mod on hub.flowpipe.io. 
| `enabled`       | Boolean | Optional | Enable or disable the trigger.  A disabled trigger will not fire, but it will retain its history and configuration.  Default is `true`.
| `param`         | Block   | Optional | A [param](#parameters) block that defines the parameters that can be passed into the trigger.
| `tags` | Map | Optional | A map of key:value metadata for the mod, used to categorize, search, and filter.   
| `title`         | String  | Optional | Display title for the step.


## Parameters

One or more `param` blocks may optionally be used in a trigger to define parameters that the trigger accepts. 

```hcl
param "url" {
  type    = string
  default = "http://api.open-notify.org/astros"
}
```

### Arguments


| Name          | Type    | Description
|---------------|---------|--------------------------
| `default`     | Any     | A value to use if no argument is passed for this parameter when the query is run.
| `description` | String  | A description of the parameter.
| `type`        | String   | The data type of the parameter: `string`, `number`, `bool`, `list`, `map`, `any` (default `any`). 

----

## More examples

The `schedule` argument may be a named interval (`hourly`, `daily`, `weekly`, `5m`, `10m`, `15m`, `30m`, `60m`, `1h`, `2h`, `4h`, `6h`, `12h`, `24h`).  When using a named interval, Flowpipe will automatically jitter the time within the interval, which helps avoid resource contention.

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "hourly"
    pipeline = pipeline.my_pipe
}
```

The `schedule` argument may be a custom schedule in cron syntax.  No jitter is added to cron schedules; they will run at the time specified.

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "*/30 * * * *"
    pipeline = pipeline.my_pipe
}
```
