---
id: learn
title: Learn Flowpipe
sidebar_label: Learn Flowpipe
slug: /
---

# Learn Flowpipe

Flowpipe allows you to create "pipelines as code", defining workflows and other tasks that are performed in a sequence.

## Creating your first pipeline

Getting started is easy!  If you haven't already done so, [download and install Flowpipe](/downloads).


Flowpipe pipelines and triggers are packaged into [mods](/docs/build/index), and Flowpipe requires a mod to run.  Let's create a new directory for our mod, and then run `flowpipe mod init` to initialize it:
```bash
mkdir learn_flowpipe
cd learn_flowpipe
flowpipe mod init
```

The `flowpipe mod init` command creates a file named `mod.fp` in the directory.  This file contains a `mod` definition for our new mod:
```hcl
mod "local" {
  title = "learn_flowpipe"
}
```
You can customize the [mod definition](/docs/flowpipe-hcl/mod) if you like, but the default is sufficient for our purposes.

Let's create our first pipeline.  

Flowpipe mods are written in HCL.  When Flowpipe runs, it will load the mod from the working directory and will read all files with the `.fp` extension from the directory and its subdirectories recursively.  Create a file named `learn.fp` and add the following code:

```hcl
pipeline "learn_flowpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }
}
```

A Flowpipe [pipeline](/docs/flowpipe-hcl/step/pipeline) is a sequence of steps to do work. This snippet creates a pipeline called `learn_flowpipe` that has a single [http step](/docs/flowpipe-hcl/step/http), and a single [output](/docs/flowpipe-hcl/step/pipeline#outputs).

Let's run it!

```bash
flowpipe pipeline run learn_flowpipe
```

```bash
[flowpipe] Execution ID: exec_clia89jjtoje2jue3eq0
[learn_flowpipe] Starting pipeline
[learn_flowpipe.get_ipv4] Starting http: GET https://api.ipify.org?format=json
[learn_flowpipe.get_ipv4] Complete: 200 166ms
[learn_flowpipe] Output ip_address = 74.125.224.72 
[learn_flowpipe] Complete 186ms exec_clia89jjtoje2jue3eq0
```

Flowpipe runs the pipeline and prints information about the currently executing steps. When the pipeline run completes, Flowpipe prints the pipeline's outputs. 


## Using mods


Flowpipe's modular design allows you to build pipelines from other pipelines.  Let's install the `reallyfreegeoip` mod:

```bash
flowpipe mod install https://github.com/turbot/flowpipe-mod-reallyfreegeoip
```
```bash
Installed 1 mod:

local
└── github.com/turbot/flowpipe-mod-reallyfreegeoip@v0.1.0
```

The mod is installed into the `.flowpipe/mods` subdirectory, and a dependency is added to your `mod.fp`.

Now that the mod is installed, you should see its pipelines:
```bash
flowpipe pipeline list
```
```bash
MOD                    NAME                                           DESCRIPTION
mod.local              local.pipeline.learn_flowpipe                  
mod.reallyfreegeoip    reallyfreegeoip.pipeline.get_ip_geolocation    Get geolocation data for an IPv4 or IPv6 address.
```

You can run pipelines from the dependency mod on the command line:
```bash
flowpipe pipeline run reallyfreegeoip.pipeline.get_ip_geolocation --arg ip_address=35.236.238.30
```
```bash
[flowpipe] Execution ID: exec_clmt595o402fipd4s2lg
[get_ip_geolocation] Starting pipeline
[get_ip_geolocation.get_ip_geolocation] Starting http: GET https://reallyfreegeoip.org/json/35.236.238.30
[get_ip_geolocation.get_ip_geolocation] Complete: 200 1s
[get_ip_geolocation] Output geolocation = {
  "city": "",
  "country_code": "US",
  "country_name": "United States",
  "ip": "35.236.238.30",
  "latitude": 38.6583,
  "longitude": -77.2481,
  "metro_code": 0,
  "region_code": "VA",
  "region_name": "Virginia",
  "time_zone": "America/New_York",
  "zip_code": ""
}
[get_ip_geolocation] Complete 1s exec_clmt595o402fipd4s2lg
```

While running the dependency pipelines directly in the CLI is useful, the real power is the ability to compose pipelines from other pipelines.  Let's add a [pipeline step](/docs/flowpipe-hcl/step/pipeline) to take our ip address and look up our geo-location information.

```hcl
pipeline "learn_flowpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  step "pipeline" "get_geo" {
    pipeline = reallyfreegeoip.pipeline.get_ip_geolocation
    args = {
      ip_address = step.http.get_ipv4.response_body.ip
    }
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }

  output "latitude" {
    value = step.pipeline.get_geo.output.geolocation.latitude
  }

  output "longitude" {
    value = step.pipeline.get_geo.output.geolocation.longitude
  }
}
```

Notice that we used the IP address from the first step (`step.http.get_ipv4.response_body.ip`) as an argument to the second step.  Flowpipe automatically detects this dependency and runs the steps in the correct order!  

Let's add a couple more steps to get the weather forecast for our location.

```hcl
pipeline "learn_flowpipe" {
  step "http" "get_ipv4" {
    url = "https://api.ipify.org?format=json"
  }

  step "pipeline" "get_geo" {
    pipeline = reallyfreegeoip.pipeline.get_ip_geolocation

    args = {
      ip_address = step.http.get_ipv4.response_body.ip
    }
  }

  step "http" "get_weather" {
    url = join("", [
      "https://api.open-meteo.com/v1/forecast",
      "?latitude=${step.pipeline.get_geo.output.geolocation.latitude}",
      "&longitude=${step.pipeline.get_geo.output.geolocation.longitude}",
      "&current=temperature",
      "&forecast_days=1",
      "&daily=temperature_2m_min,temperature_2m_max,precipitation_probability_mean",
      "&temperature_unit=${step.pipeline.get_geo.output.geolocation.country_code == "US" ? "fahrenheit" : "celsius"}"
    ])
  }
 
  step "transform" "friendly_forecast" {
    value = join("", [
      "It is currently ",
      step.http.get_weather.response_body.current.temperature,
      step.http.get_weather.response_body.current_units.temperature,
      ", with a high of ",
      step.http.get_weather.response_body.daily.temperature_2m_max[0],
      step.http.get_weather.response_body.daily_units.temperature_2m_max,
      " and a low of ",
      step.http.get_weather.response_body.daily.temperature_2m_min[0],
      step.http.get_weather.response_body.daily_units.temperature_2m_min,
      ".  There is a ",
      step.http.get_weather.response_body.daily.precipitation_probability_mean[0],
      step.http.get_weather.response_body.daily_units.precipitation_probability_mean,
      " chance of precipitation."
    ])
  }

  output "ip_address" {
    value = step.http.get_ipv4.response_body.ip
  }

  output "latitude" {
    value = step.pipeline.get_geo.output.geolocation.latitude
  }

  output "longitude" {
    value = step.pipeline.get_geo.output.geolocation.longitude
  }

  output "forecast" {
    value = step.transform.friendly_forecast.value
  }
}
```


<!--
***TO DO:   SEND IT VIA SLACK***
[wait for `credential` implementation]
-->