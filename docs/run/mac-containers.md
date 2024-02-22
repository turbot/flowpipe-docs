---
title: Using Containers on macOS
sidebar_label: Using Containers on macOS
---

# Using Containers on macOS

Turbot offers a container image with Flowpipe pre-installed, based on debian-slim. This minimal setup includes the `flowpipe` binary and is configured for Docker-outside-of-Docker (DooD), enabling the container to utilize the host system's Docker daemon.

The image is published to GitHub Container Registry:

```bash
docker pull ghcr.io/turbot/flowpipe:latest
```

## Running locally

To run flowpipe, you can simply run the container:

```bash
docker run -it --rm --name flowpipe ghcr.io/turbot/flowpipe:latest
```

## Configuring Flowpipe for Host Docker Integration

For macOS users, interacting with the Docker socket is facilitated through the [Docker socket from the extension backend](https://docs.docker.com/desktop/extensions-sdk/guides/use-docker-socket-from-backend/), allowing Flowpipe to communicate with the host's Docker daemon. To run Flowpipe on macOS with the appropriate Docker socket binding, use the following command:

```bash
docker run \
  -it \
  --rm \
  --name flowpipe \
  -v /var/run/docker.sock.raw:/var/run/docker.sock \
  ghcr.io/turbot/flowpipe:latest
```

This command mounts the Docker socket into the Flowpipe container, enabling Docker-outside-of-Docker (DooD) functionality. The use of `.raw` in the volume mount specifies the actual socket path for Docker Desktop on macOS, ensuring direct communication with the Docker daemon.

## Running locally, in lieu of "standard" flowpipe install

Instead of installing Flowpipe directly onto your system, you can set up an alias to run the Flowpipe container as a local command. This method leverages Docker to execute Flowpipe in an isolated environment, simplifying the setup and ensuring consistency across different machines. Below are the aliases for macOS and Linux:

For macOS users, the alias includes a specific Docker socket path used by Docker Desktop. This ensures that Flowpipe can communicate with the Docker daemon running on macOS:

```bash
mkdir -p $HOME/fp/config

alias fp="docker run \
  -it \
  --rm \
  --name flowpipe \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config  \
  --mount type=volume,source=flowpipe_workspace,target=/workspace \
  -v /var/run/docker.sock.raw:/var/run/docker.sock \
  ghcr.io/turbot/flowpipe"
```

The container entrypoint is the `flowpipe` command, so once you've set up an alias, you can effectively run the same commands.

You can install mods:

```bash
fp mod install github.com/turbot/flowpipe-mod-slack
```

```bash
Installed 1 mod:

local
└── github.com/turbot/flowpipe-mod-slack@v0.2.0
```

List the available pipelines:

```bash
fp pipeline list
```

Update the credentials with a valid OAuth Slack token:

```bash
echo '
credential "slack" "default" {
  token = "xoxb-oauth-slack-token"
}' >> $HOME/fp/config/credentials.fpc
```

Once configured, Slack pipelines can be run as seen in next example:

```bash
fp pipeline run slack.pipeline.list_channels
```

```bash
[flowpipe] Execution ID: exec_gn9afdu1g8js234khug0
[list_channels] Starting pipeline
[list_channels.list_channels[0]] Starting http: GET https://slack.com/api/conversations.list?limit=200&
[list_channels.list_channels[0]] Complete: 200 442ms
[list_channels.list_channels[1]] Starting http: GET https://slack.com/api/conversations.list?limit=200&&cursor=dGVhbTpDMDFENUVSMUJESg==
[list_channels.list_channels[1]] Complete: 200 407ms
[list_channels] Output channels = [
  {
    <REDACTED>
  }
]
```

## Running Flowpipe as a server

You can run flowpipe container in daemon mode (with `-d`) to run flowpipe as a server which allows you to run pipelines triggered by webhooks.
The server can only be started in a directories that contain a flowpipe mod.
Exposing the flowpipe trigger port (7103) allows you to connect to the instance with 3rd party tools.

For example:

```bash
# Create a local flowpipe configuration directory
mkdir -p $HOME/fp/config

# Clone the flowpipe sample repository into the local workspace directory
git clone https://github.com/turbot/flowpipe-samples.git $HOME/fp/workspace

# Install dependent mods for the notify sample
# `--listen network` is currently here to get around the error where the network defaults to `local`.
docker run \
  --rm \
  --name flowpipe-detached \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$HOME/fp/workspace,target=/workspace \
  ghcr.io/turbot/flowpipe mod install \
    --mod-location /workspace/software_development/notify_slack_new_github_release


# Start the server in detached mode
docker run \
  -p 7103:7103 \
  -d \
  -it \
  --rm \
  --name flowpipe-detached \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$HOME/fp/workspace,target=/workspace \
  ghcr.io/turbot/flowpipe server \
    --var slack_channel=example-channel \
    --mod-location /workspace/software_development/notify_slack_new_github_release \
    --listen network
```

Once the server is running, we need to obtain the HTTP web endpoint address.
To retrieve this address:

```bash
docker logs flowpipe-detached
```

```bash
2024-02-22 01:16:18 [flowpipe] Started v0.2.3
2024-02-22 01:16:18 [flowpipe] Listening on :7103
2024-02-22 01:16:18 [flowpipe] Loaded mod.notify_slack_new_github_release
2024-02-22 01:16:18 [trigger.http.github_webhook_release_events] Enabled HTTP POST http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/unique_id
2024-02-22 01:16:18 [flowpipe] Press Ctrl+C to exit
```

Which in this case is: `http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/unique_id`

The web endpoint can be triggered using `curl`, :

```bash
curl -X POST \
  -H "X-Github-Event: example" \
  -H "Content-Type: application/json" \
  -d '{"action": "some_action"}' \
  http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/unique_id
```

Checking the logs of the container to check if the trigger worked:

```bash
docker logs flowpipe-detached
```

```bash
2024-02-22 01:16:18 [flowpipe] Started v0.2.3
2024-02-22 01:16:18 [flowpipe] Listening on :7103
2024-02-22 01:16:18 [flowpipe] Loaded mod.notify_slack_new_github_release
2024-02-22 01:16:18 [trigger.http.github_webhook_release_events] Enabled HTTP POST http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/768062030f98f32009ea0639ed333b1ec5c141697fe1db7a77d3eb245cc1545f
2024-02-22 01:16:18 [flowpipe] Press Ctrl+C to exit
2024-02-22 01:16:58 exec_cnba12mf7moc73bdhk5g [trigger.http.github_webhook_release_events] fired, executing router_pipeline
2024-02-22 01:16:58 exec_cnba12mf7moc73bdhk5g [router_pipeline] Starting
2024-02-22 01:16:59 exec_cnba12mf7moc73bdhk5g [router_pipeline] Output post_message_check = Message sent to example-channel
2024-02-22 01:16:59 exec_cnba12mf7moc73bdhk5g [router_pipeline] Complete 7ms exec_cnba12mf7moc73bdhk5g
```

### Running in read-only mode

It is possible to run the flowpipe container with a read-only root filesystem, but note the following:

- `/tmp` must be writable (mount with tmpfs)
- config (`/home/flowpipe/.flowpipe/config`) must be writable
- workspace working directory internal (`<working_directory>/.flowpipe/internal`) must be writable
- workspace working directory store (`<working_directory>/.flowpipe/store`) must be writable

```bash
mkdir -p $HOME/fp/config

# Clone the flowpipe sample repository into the local workspace directory
git clone https://github.com/turbot/flowpipe-samples.git $HOME/fp/workspace

# Install dependent mods for the notify sample
# `--listen network` is currently here to get around the error where the network defaults to `local`.
docker run \
  --rm \
  --name flowpipe-detached \
  --read-only \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$HOME/fp/workspace/software_development/notify_slack_new_github_release,target=/workspace/software_development/notify_slack_new_github_release \
  ghcr.io/turbot/flowpipe mod install \
    --mod-location /workspace/software_development/notify_slack_new_github_release


# Start the server in detached mode
docker run \
  -p 7103:7103 \
  -d \
  -it \
  --rm \
  --name flowpipe-detached \
  --read-only \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$HOME/fp/workspace/software_development/notify_slack_new_github_release,target=/workspace/software_development/notify_slack_new_github_release \
  ghcr.io/turbot/flowpipe server \
    --var slack_channel=example-channel \
    --mod-location /workspace/software_development/notify_slack_new_github_release \
    --listen network
```

## Running a batch job with derived image

You may want to run flowpipe in a batch job, either from a scheduler such as cron, or as part of a continuous integration workflow.
In such a case you may want to create your own image based on the flowpipe standard image, with mods pre-installed.

For example:

```dockerfile
# Start from the base image
FROM ghcr.io/turbot/flowpipe

# Update and install git
RUN apt-get update -y && apt-get install -y git

# Switch to flowpipe user for cloning the repository
USER flowpipe
WORKDIR /workspace
RUN git clone --depth 1 https://github.com/turbot/flowpipe-mod-aws.git /workspace

# Switch back to root as further root-level operations are needed for docker-entrypoint.sh
USER root
```

After building this docker file as `flowpipe-aws`:

```bash
docker build . -t flowpipe-aws
```

When running, you want to pass credentials via environment variables instead of configuration file:

```bash
# build it
docker build -t flowpipe-aws .

# run it
docker run \
  -it \
  --rm \
  --name flowpipe-aws \
  --mount type=bind,source="${PWD}",target=/output \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
  -e AWS_REGION=us-east-1 \
  -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
   flowpipe-aws pipeline run aws.pipeline.list_s3_buckets --arg 'region=us-east-1'
```
