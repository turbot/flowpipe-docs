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
        query = "(steampipe OR steampipe.io OR github.com/turbot) -tomathy -GutturalSteve -\"steampipe alley\""
    }

}
```


## Arguments

| Argument        | Type    | Optional?  | Description
|-----------------|---------|------------|-----------------
| `pipeline`      | Pipeline Reference | Required | A reference to a `pipeline` resource to start when this trigger runs.  
| `schedule`      | String  | Required   | [Schedule](#more-examples) to run the query. This may be a named interval (`hourly`, `daily`, `weekly`), a custom schedule in cron syntax, or a duration string. 
| `args`	      | Map	    | Optional	  | A map of arguments to pass to the pipeline.
| `description`   |  String | Optional   | A string containing a short description of the step. 
| `documentation` | String | Optional | A markdown string containing a long form description, used as documentation for the mod on hub.flowpipe.io. 
| `enabled`       | Boolean | Optional   | Enable or disable the trigger.  A disabled trigger will not fire, but it will retain its history and configuration.  Default is `true`.
| `tags` | Map | Optional | A map of key:value metadata for the mod, used to categorize, search, and filter.   
| `title`         | String  | Optional | Display title for the step.




## More examples

The `schedule` argument may be a named interval (`hourly`, `daily`, `weekly`)

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "hourly"
    pipeline = pipeline.my_pipe
}
```
or  a custom schedule in cron syntax:

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "*/30 * * * *"
    pipeline = pipeline.my_pipe
}
```


or a [Go duration string](https://pkg.go.dev/time#Duration). The duration string specifies the number and type of units. Valid time units are s, m, h:


```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "2h"
    pipeline = pipeline.my_pipe
}
```

and you can combine units:

```hcl
trigger "schedule" "my_hourly_trigger" {
    schedule = "2h30m"
    pipeline = pipeline.my_pipe
}
```