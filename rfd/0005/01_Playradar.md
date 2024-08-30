---
authors: dennislwm
title: [SilverBullet](https://silverbullet.md/SilverBullet)
description: A note-taking web application stored as Markdown files.
piped_from: Idea
piped_to: [Playradar](https://docs.google.com/spreadsheets/d/1Kgja1_RKYJB3a7DuyiPlXQgB0_agLR-AVUx_VkgtR3o/edit?gid=0#gid=0)
quadrant: platforms
rings: trial
org_size: small
---

# Introduction

[SilverBullet](https://silverbullet.md/SilverBullet) is a note-taking web application stored as Markdown files.

1. annotate your notes using Frontmatter.
2. support two client modes Online and Sync.
3. create reusable Templates using a Templates Language.
4. build applications in your note apps using End-User Programming.

# Installation options

Installing SilverBullet as a (local) web server is pretty straightforward; you run the SilverBullet server process on your machine, then connect to it locally from your browser.

You have a few options here:

1. Installation via Docker (which is covered in this document).
2. Installation via [Deno](https://silverbullet.md/Install/Deno).

## Installation via Docker

Conveniently, SilverBullet is published as a docker image on [DockerHub](https://hub.docker.com/r/zefhemel/silverbullet). The image comes in two flavours:
* 64-bit Intel
* 64-bit ARM

A few key things to know about the SilverBullet container image:
* The container binds to port `3000`.
* The container uses `/space` as the root folder. You can connect a docker volume, or a host folder to this.
* The container supports basic authentication for a single user using a docker environment variable `SB_USER`, e.g. `SB_USER=user:password`.
* The container will detect the UNIX owner (UID and GID) of the folder mapped into `/space` and run the server process with the same UID and GID so that permissions will just magically work.
* Check out [[Configuration](https://silverbullet.md/Install/Configuration)] for more interesting environment variables you can set.

## Security

The default security settings for SilverBullet is potentially unsafe.

* `SB_SHELL_BACKEND`: Enable/disable running of shell commands from plugs, defaults to `local`(enabled), set to `off` to disable. It is only enabled when using a local folder for [Storage](https://silverbullet.md/%23Storage).
* `SB_SPACE_SCRIPT`: Enable/disable [Space Script](https://silverbullet.md/Space%20Script). Defaults to `on`, set to `off`to disable this feature altogether.

### Run the container as a background service using Docker run

1. Open a bash terminal and run the following commands:

```sh
mkdir /usr/home/space
sudo docker run -d --restart unless-stopped --name objSilverBullet \
-e SB_USER='admin:password' \
-e SB_SHELL_BACKEND=off \
-e SB_SPACE_SCRIPT=off \
-p 8090:3000 \
-v /usr/home/space:/space \
zefhemel/silverbullet
```

### Run the container as a background service using Docker compose

1. Create a file `docker-compose-silverbullet.yml`.

```yml
services:
  silverbullet:
    image: zefhemel/silverbullet
    restart: unless-stopped
    environment:
    - SB_USER=admin:password
    - SB_SHELL_BACKEND=off
    - SB_SPACE_SCRIPT=off
    volumes:
      - /usr/home/space:/space
    ports:
      - 8090:3000
```

2. Open a shell terminal and run the command.

```sh
docker-compose -f docker-compose-silverbullet.yml up -d
```