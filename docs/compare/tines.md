---
title: Flowpipe versus Tines
sidebar_label: Tines
---


# Flowpipe versus Tines

**Flowpipe**: Automate cloud operations. Coordinate people and pipelines. Build workflows as code.

**Tines**: A workflow builder for technical teams.

|                    | **Flowpipe**                                                       | **Tines**                                                               |
|--------------------|--------------------------------------------------------------------|-------------------------------------------------------------------------|
| **Audience**       | DevOps engineers who automate operational workflows               | IT professionals, security teams, and business users who automate workflows |
| **Deployment**     | Single binary runs locally or elsewhere                            | SaaS                                                                    |
| **Programming style** | Workflow-as-code, using HCL                                      | Visual builder                                                          |
| **Pre-built automations** | [Yes](https://hub.powerpipe.io)                             | [Yes](https://www.tines.com/library/stories)                           |
| **Parallel execution** | [Yes](https://flowpipe.io/docs/build/write-pipelines/iteration#for_each) | Yes |
| **Composable modules** | [Yes](https://flowpipe.io/docs/build/mod-dependencies)          | No                                                                      |
| **Terms**          | Open source                                                        | Proprietary, free limited self-hosting, call for pricing                |

## When to choose Flowpipe over Tines
Tines is a good choice for teams seeking a no-code, versatile automation platform for a wide range of tasks and workflows across different teams. Flowpipe is the preferred choice for DevOps engineers who automate cloud operations, coordinate people and pipelines, and build workflows as code.

## About Flowpipe
Flowpipe is workflow for DevOps. Use it to automate cloud operations, coordinate people and pipelines, and build workflows as code.

Use prebuilt workflows. The [Flowpipe Hub](https://hub.flowpipe.io/) provides ready-to-run pipelines for common DevOps scenarios.

Build custom workflows. Pipelines are [composable](https://flowpipe.io/docs/build/mod-dependencies). Extend the prebuilt workflows, or build your own from scratch. Any pipeline can call, or be called by, any other.

Leverage diverse step types. A step can [make an HTTP call](https://flowpipe.io/docs/flowpipe-hcl/step/http), [gather human input](https://flowpipe.io/docs/flowpipe-hcl/step/input), [send a message](https://flowpipe.io/docs/flowpipe-hcl/step/message), [run a query](https://flowpipe.io/docs/flowpipe-hcl/step/query), [run a pipeline](https://flowpipe.io/docs/flowpipe-hcl/step/pipeline), [use a container](https://flowpipe.io/docs/flowpipe-hcl/step/container), or [run a lambda function](https://flowpipe.io/docs/flowpipe-hcl/step/function).

React to events. A [trigger](https://flowpipe.io/docs/flowpipe-hcl/trigger) runs a pipeline when an event occurs, via a [webhook](https://flowpipe.io/docs/flowpipe-hcl/trigger/http), [query](https://flowpipe.io/docs/flowpipe-hcl/trigger/query), or [schedule](https://flowpipe.io/docs/flowpipe-hcl/trigger/schedule).

Use code, not clicks. Pipelines are [HCL](https://flowpipe.io/docs/build): version-controlled, shareable, easy to edit — designed for the way you work.

Deploy anywhere. Use it locally, or deploy it in CI/CD pipelines.

<div className="flex justify-center">
<iframe
    className="youtube-video"
    src="https://flowpipe.io/videos/short_demo_loop.mp4"
    frameBorder="0"
    allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowFullScreen
    title="Human interaction with Flowpipe"
>
</iframe>
</div>

## Get Started with Flowpipe
First [download Flowpipe](https://flowpipe.io/downloads), run through the [tutorial](https://flowpipe.io/docs/build), and check out the [libraries](https://hub.flowpipe.io/?type=library) and [samples](https://hub.flowpipe.io/?type=library). Then build some pipelines that automate your DevOps workflows. For a team experience, try Flowpipe in [Turbot Pipes](https://turbot.com/pipes).
