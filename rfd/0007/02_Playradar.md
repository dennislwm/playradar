---
authors: dennislwm
title: AnythingLLM
description: A full-stack application that enables you to turn any document, resource, or piece of content into context that any LLM can use as references during chatting.
piped_from: Playradar
piped_to: Playradar
quadrant: platforms
rings: trial
org_size: small
---

# Introduction

[AnythingLLM][r01] is a full-stack application that enables you to turn any document, resource, or piece of content into context that any LLM can use as references during chatting.

1. allows you to pick and choose which LLM or Vector Database you want to use as well as supporting multi-user management and permissions;
2. Workspace functions a lot like a thread, but with the addition of containerization of your documents;

AnythingLLM has replaced LibreChat due to its fairly simple configuration, and use of an embedded vector database, with no external service dependencies.

# Installation Options

You have a few options here:

* Installation via [Desktop Application][r02] for Linux, Mac, and Windows.
* Installation via Docker (which is covered in this document).

## Installation via Docker

AnythingLLM is published as an image `mintplexlabs/anythingllm` on DockerHub. The image comes in two flavours:

* `linux/amd64`
* `linux/arm64`

A few key things to know about the container image:

* The container binds to port `3001`.
* The container uses `/app/server/storage` as the storage folder. You can connect a docker volume, or a host folder to this.
* The container uses `/app/server/.env` as the `.env` file. You can connect a host file to this.
* The container uses an environment variable `STORAGE_DIR` to specify the default storage folder, i.e. `/app/server/storage`.
* The container uses UID and GID of `1000` by default.
* The container supports authentication and rule-based access for authorisation.
* The container comes with an embedded vector database.

## Run the container as a background service using Docker run

1. Open a bash terminal and run the following commands:

```sh
export STORAGE_LOCATION="$HOME/data/anythingllm" && \
mkdir -p $STORAGE_LOCATION && \
touch "$STORAGE_LOCATION/.env" && \
sudo docker run -d --rm --restart unless-stopped --name objAnythingllm \
-p 3001:3001 \
-e STORAGE_DIR="/app/server/storage" \
-v ${STORAGE_LOCATION}:/app/server/storage \
-v ${STORAGE_LOCATION}/.env:/app/server/.env \
--cap-add SYS_ADMIN \
mintplexlabs/anythingllm
```

## Run the container as a background service using Docker compose

1. Create a file `docker-compose-anythingllm.yml`.

```yml
services:
  anythingllm:
    image: mintplexlabs/anythingllm
    restart: unless-stopped
    cap_add:
      - SYS_ADMIN
    environment:
      - STORAGE_DIR=/app/server/storage
    volumes:
      - $HOME/data/anythingllm:/app/server/storage
      - $HOME/data/anythingllm/.env:/app/server/.env
    ports:
      - 3001:3001
```

2. Open a shell terminal and run the command.

```sh
docker-compose -f docker-compose-anythingllm.yml up -d
```

# Security

AnythingLLM Docker supports two types of use cases: single-user and multi-user mode.

## Single User

You can password protect the instance by toggling on the "Password Protect Instance" option. This will display an input where you can enter the password to protect the instance.

## Multi-User

To enable multi-user mode, toggle on the "Enable multi-user mode" option. This will display an input where you can enter the username and password for the first administrator account.

By default, you will create the administrator account, which has the highest level of privilege.

Once set, you will be logged out so you can log in with the new password.

### User Roles

* **Admin**: Full access to the entire system.
* **Manager**: Can view all workspaces and manage all properties except for settings for LLM, Embedder, and Vector database.
* **Default**: Can only send chats to workspaces they are explicitly added to. Cannot see or edit any workspaces or system settings.


[r01]: https://anythingllm.com/
[r02]: https://anythingllm.com/download