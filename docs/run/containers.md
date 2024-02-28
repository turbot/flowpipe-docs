---
title: Container Usage Guide
sidebar_label: Container Usage Guide
---

# Container Usage Guide

Turbot's Flowpipe is available as a Docker container image, pre-configured for seamless integration with Docker environments. This guide provides instructions on how to deploy and run Flowpipe using Docker, including configurations for different operating systems and ensuring proper user permissions.

## Getting Started

### Pulling the Flowpipe Image

First, obtain the Flowpipe container image from the GitHub Container Registry by executing the following command:

```bash
docker pull ghcr.io/turbot/flowpipe:latest
```

### Running Flowpipe Locally

To start using Flowpipe immediately, you can run the container with a simple command:

```bash
docker run \
    -it \
    --rm \
    --name flowpipe \
    ghcr.io/turbot/flowpipe:latest
```

## Configurations for Enhanced Security and Functionality

### Setting Environment Variables for Linux User Permissions

Deploying Flowpipe on a Linux system may require setting specific environment variables to manage file ownership and Docker socket access securely. Adding `-e USER_UID=$(id -u)` and `-e USER_GID=$(id -g)` to your Docker run command aligns the container's file and process ownership with that of the host system. This adjustment ensures:

- **Accurate file ownership** in mounted volumes.
- **Effective Docker socket access**, enabling Docker-outside-of-Docker (DooD) operations by matching the container user's permissions with the host's Docker daemon requirements.

Example command incorporating these environment variables:

```bash
docker run \
    -it \
    --rm \
    --name flowpipe \
    -e USER_UID=$(id -u) \
    -e USER_GID=$(id -g) \
    ghcr.io/turbot/flowpipe:latest
```

## Integrating Flowpipe with the Host's Docker Daemon

To utilize Flowpipe's capability of executing Docker container steps within your workflows, the Flowpipe container must access the host's Docker daemon.

### macOS Docker Socket Configuration

On macOS, Docker socket communication is achieved by mounting the Docker socket from the extension backend into the Flowpipe container. Utilize the following command to set up this configuration:

```bash
docker run \
    -it \
    --rm \
    --name flowpipe \
    -v /var/run/docker.sock.raw:/var/run/docker.sock \
    ghcr.io/turbot/flowpipe:latest
```

This command facilitates Docker-outside-of-Docker (DooD) functionality by ensuring the Flowpipe container can directly interact with the host's Docker daemon, using the specific `.raw` socket path designated for Docker Desktop on macOS.

### Linux Docker Socket Configuration

For Linux users, the following command configures Flowpipe to access the host's Docker daemon, enabling execution of Docker container steps within workflows. This setup includes the environment variables for managing user permissions, as discussed earlier:

```bash
docker run \
    -it \
    --rm \
    --name flowpipe \
    -e USER_UID=$(id -u) \
    -e USER_GID=$(id -g) \
    -v /var/run/docker.sock:/var/run/docker.sock \
    ghcr.io/turbot/flowpipe:latest
```

Binding the host's Docker socket to the container allows Flowpipe to perform Docker-outside-of-Docker operations, facilitating seamless integration and execution of Docker-based workflows.

## Running Locally as an Alternative to Standard Installation

To simplify the usage of Flowpipe as if it were locally installed, you can create a Docker alias. This alias enables running the Flowpipe container as a local command. It binds your active directories to the '/workspace' directory within the running container.

### Preparation

Start by creating essential directories for Flowpipe's configuration and workspace for our examples:

```bash
mkdir -p $HOME/fp/config
mkdir -p $HOME/fp/workspace
```

Below are instructions on how to set up aliases for both macOS and Linux users:

### macOS Setup

For macOS users, the alias includes a specific Docker socket path used by Docker Desktop. This ensures that Flowpipe can communicate with the Docker daemon running on macOS. Open your terminal and run the following commands:

```bash
alias fp="docker run \
    -it \
    --rm \
    --name flowpipe \
    --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
    --mount type=bind,source=$(pwd),target=/workspace \
    -v /var/run/docker.sock.raw:/var/run/docker.sock \
    ghcr.io/turbot/flowpipe"
```

### Linux Setup

For Linux users, the setup is slightly different to accommodate the Linux Docker socket and passing across local user information to the container. 
Run the following commands in your terminal:

```bash
alias fp="docker run \
  -it \
  --rm \
  --name flowpipe \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$(pwd),target=/workspace \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -e USER_UID=$(id -u) \
  -e USER_GID=$(id -g) \
  ghcr.io/turbot/flowpipe"
```

### Using Flowpipe with Docker

Once the alias is set up, you can start using Flowpipe commands as though the application were installed locally on your system.

Navigate to the directory `$HOME/fp/workspace`:

```bash
cd $HOME/fp/workspace
```

Following section provide some common commands to get you started:

#### Installing Mods

To install mods, use the `fp mod install` command. For example, to install the Slack mod:

```bash
fp mod install github.com/turbot/flowpipe-mod-slack
```

You should see an output similar to:

```bash
Installed 1 mod:

local
└── github.com/turbot/flowpipe-mod-slack@v0.2.0
```

After running this command, the mod will be installed in the directories for this `$HOME/fp/workspace`:

```bash
ls -al
```

```bash
total 36
drwxrwxr-x 3 user group 4096 Feb 28 14:27 .
drwxrwxr-x 4 user group 4096 Feb 28 13:59 ..
drwxr-xr-x 3 user group 4096 Feb 28 14:27 .flowpipe
-rw-r--r-- 1 user group  234 Feb 28 14:27 .mod.cache.json
-rw-r--r-- 1 user group  128 Feb 28 14:27 mod.fp
```

#### Listing Available Pipelines

To list all available pipelines, use:

```bash
fp pipeline list
```

```bash
MOD      NAME                                           DESCRIPTION
slack    slack.pipeline.archive_channel                 Archives a conversation.
slack    slack.pipeline.create_channel                  Initiates a public or private channel-based conversation.
slack    slack.pipeline.delete_message                  Deletes a message.
slack    slack.pipeline.delete_scheduled_message        Delete a pending scheduled message from the queue.
slack    slack.pipeline.get_channel                     Retrieve information about a conversation.
slack    slack.pipeline.get_channel_history             Fetches a conversation's history of messages and events.
slack    slack.pipeline.get_channel_id                  Get the ID from the channel name.
slack    slack.pipeline.get_current_user                Retrieve the current user's or bot's profile information, including their custom status.
slack    slack.pipeline.get_message_permalink           Retrieve a permalink URL for a specific extant message.
slack    slack.pipeline.get_user                        Retrieve a user's profile information, including their custom status.
slack    slack.pipeline.invite_users_to_channel         Invites users to a channel.
slack    slack.pipeline.list_channels                   Lists all channels in a Slack team.
slack    slack.pipeline.list_scheduled_messages         List of scheduled messages.
slack    slack.pipeline.open_message                    Open or resume a direct message or multi-person direct message.
slack    slack.pipeline.post_message                    Sends a message to a channel.
slack    slack.pipeline.post_message_to_webhook         Post messages from apps into Slack using incoming webhooks.
slack    slack.pipeline.schedule_message                Schedule a message to be sent to a channel.
slack    slack.pipeline.test_create_channel             Test the create_channel pipeline.
slack    slack.pipeline.test_invite_users_to_channel    Test the invite_users_to_channel pipeline.
slack    slack.pipeline.test_post_message               Test the post_message pipeline.
slack    slack.pipeline.test_schedule_message           Test the schedule_message pipeline.
slack    slack.pipeline.test_update_message             Test the update_message pipeline.
slack    slack.pipeline.update_message                  Updates a message.
```

#### Configuring Credentials

To set up credentials, like a Slack OAuth token, create a credentials file named `credentials.fpc` in your configuration directory `$HOME/fp/config`. 

Populate the file with the following content:

```bash
echo '
credential "slack" "default" {
  token = "xoxb-your-token-here"
}' >> $HOME/fp/config/credentials.fpc
```

Replacing "xoxb-your-token-here" with a valid Slack token, which should have the required `Bot Token Scope` permission of `users.profile:read`.

#### Running Pipelines

To run a pipeline, for example, getting the current Slack user:

```bash
fp pipeline run slack.pipeline.get_current_user
```

Which should generate similar output, provided the credentials are valid:

```bash
[flowpipe] Execution ID: exec_cnf9p3kf05cs73c3s290
[get_current_user] Starting pipeline
[get_current_user.get_current_user] Starting http: GET https://slack.com/api/users.profile.get
[get_current_user.get_current_user] Complete: 200 454ms
[get_current_user] Output profile = {
  "always_active": false,
  "api_app_id": "A00BB11C222",
  "avatar_hash": "ge1111b22222",
  "bot_id": "B00AAAAAAAA",
  "display_name": "",
  "display_name_normalized": "",
  "fields": {},
  "first_name": "Flowpipe",
  "image_192": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=192&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-192.png",
  "image_24": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=24&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-24.png",
  "image_32": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=32&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-32.png",
  "image_48": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=48&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-48.png",
  "image_512": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=512&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-512.png",
  "image_72": "https://secure.gravatar.com/avatar/e4253b14532c67eadf403edc86243953.jpg?s=72&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0008-72.png",
  "last_name": "Test Slack App",
  "phone": "",
  "real_name": "Flowpipe Test Slack App",
  "real_name_normalized": "Flowpipe Test Slack App",
  "skype": "",
  "status_emoji": "",
  "status_emoji_display_info": [],
  "status_expiration": 0,
  "status_text": "",
  "status_text_canonical": "",
  "title": ""
}
```

## Running Flowpipe as a Server

Running Flowpipe as a server is used for executing pipelines triggered by webhooks and integrations. The server can only be started in directories that contain a Flowpipe mod. The container exposes the Flowpipe trigger port (7103) allows you to connect to the instance with 3rd party tools.
It is possible to run the container in the background as a detached container which we will discuss later.

### Preparation

Following the steps on how to in section [Running Locally as an Alternative to Standard Installation](#running-locally-as-an-alternative-to-standard-installation) to set up the `fp` alias, we will set up a server by using an existing example mod found in the public Github repository [flowpipe-samples](https://github.com/turbot/flowpipe-samples).
The specific mod that we will be using is found in the sub-directory `software_development/notify_slack_new_github_release` of the repository.

In order for the server to run, we will have to install the mods dependencies which has to be set up before we can start the Flowpipe server.

Firstly we need to create the directories for this example:

```bash
mkdir -p $HOME/fp/config
mkdir -p $HOME/fp/workspace
```

Followed by cloning the `flowpipe-samples` repository locally and then navigate to the sub-directory `software_development/notify_slack_new_github_release`:

```bash
git clone https://github.com/turbot/flowpipe-samples.git $HOME/fp/workspace/flowpipe-samples
cd $HOME/fp/workspace/flowpipe-samples/software_development/notify_slack_new_github_release
```

Using the previous `fp` alias that we set up, run the command to install the mod's dependencies.

```bash
fp mod install
```

```bash
Installed 1 mod:

notify_slack_new_github_release
└── github.com/turbot/flowpipe-mod-slack@v0.1.0
```

### Running in server mode

With all preparation complete, we can start the server using the `fp` alias:

```bash
fp server
```

Which will ask you to set up the slack channel that you want to send a message.

```bash
Variables defined with no value set.
var.slack_channel
  Encoded ID or name of the Slack channel to send the message. Encoded ID is recommended. Examples: C1234567890, general, random

  Enter a value: example-channel
```

In the example, we have used the channel `example-channel` but the Slack channel it does not need to exist for this mod to work.
The output on the contain will then show that Flowpipe server has started and the output should be similar to:

```bash
2024-02-28 08:24:56 [flowpipe] Started v0.2.3
2024-02-28 08:24:56 [flowpipe] Listening on localhost:7103
2024-02-28 08:24:56 [flowpipe] Loaded mod.notify_slack_new_github_release
2024-02-28 08:24:56 [trigger.http.github_webhook_release_events] Enabled HTTP POST http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/unique_id
2024-02-28 08:24:56 [flowpipe] Press Ctrl+C to exit
```

Once the server is running, the container is actively listening to the HTTP endpoint: `http://localhost:7103/api/latest/hook/notify_slack_new_github_release.trigger.http.github_webhook_release_events/unique_id` which can be used by 3rd party applications to trigger the pipeline by calling this webhook.

### Running in server mode in detached mode

It is also possible to run the server in the background by running the container in detached mode by passing `-d` to docker when running the container. 
Due to differences in macOS or Linux, the command to start the server in detached mode will slightly differ.

The mod that we are using for this example required that we pass across the Slack channel ID. When starting the server in detached mode, we are required to use Flowpipe flag `--var`.

#### Running in server mode in detached mode on macOS

Execute the following command to start the Flowpipe server on macOS. This command mounts the necessary directories and exposes the required port without specifying user permissions:

```bash
docker run \
  -p 7103:7103 \
  -d \
  -it \
  --rm \
  --name flowpipe-detached \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$(pwd),target=/workspace \
  ghcr.io/turbot/flowpipe server \
  --var slack_channel=example-channel
```

#### Running in server mode in detached mode on Linux

On Linux, include environment variables to match the container's file permissions with the host's. This prevents permissions issues with files created by the container:

```bash
docker run \
  -p 7103:7103 \
  -d \
  -it \
  --rm \
  --name flowpipe-detached \
  --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
  --mount type=bind,source=$(pwd),target=/workspace \
  -e USER_UID=$(id -u) \
  -e USER_GID=$(id -g) \
  ghcr.io/turbot/flowpipe server \
  --var slack_channel=example-channel
```

### Setting up alias to run against the detached server

After initiating the server, establish an alias `fp` to streamline the execution of commands against the detached container:

```bash
alias fp="docker exec -it flowpipe-detached flowpipe"
```

As previously shown, you can use this `fp` alias to run Flowpipe commands against the running detached container, for example, listing all available pipelines:

```bash
fp pipeline list
```

## Running in read-only mode

It is possible to run the flowpipe container with a read-only root filesystem, but note the following:

- `/tmp` must be writable (mount with tmpfs)
- config (`/home/flowpipe/.flowpipe/config`) must be writable
- workspace working directory must be writable
- you cannot use read-only containers if you passing across the environment variables `USER_UID` or `USER_GID`

To run the container as a read only root system by passing the docker flag `--read-only`, you can create the alias for `fp` as follows:

On macOS use:

```bash
alias fp="docker run \
    -it \
    --rm \
    --read-only \
    --name flowpipe \
    --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
    --mount type=bind,source=$(pwd),target=/workspace \
    -v /var/run/docker.sock.raw:/var/run/docker.sock \
    ghcr.io/turbot/flowpipe"
```

On Linux use:

```bash
alias fp="docker run \
    -it \
    --rm \
    --read-only \
    --name flowpipe \
    --mount type=bind,source=$HOME/fp/config,target=/home/flowpipe/.flowpipe/config \
    --mount type=bind,source=$(pwd),target=/workspace \
    -v /var/run/docker.sock:/var/run/docker.sock \
    ghcr.io/turbot/flowpipe"
```

Now you can call all available command for Flowpipe as previous examples.