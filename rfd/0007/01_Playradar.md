---
authors: dennislwm
title: LibreChat
description: An Enhanced ChatGPT Clone completely open-source for self-hosting.
piped_from: Idea
piped_to: Playradar
quadrant: platforms
rings: hold
org_size: medium
---

# Introduction

[LibreChat](https://github.com/danny-avila/LibreChat) is an Enhanced ChatGPT Clone completely open-source for self-hosting.

1. UI matching ChatGPT, including Dark mode, Streaming, and latest updates;
2. integrates and enhances original client features such as conversation and message search, prompt templates and plugins;

LibreChat has been replaced by or pivoted to AnythingLLM, as LibreChat has more service dependencies and appears to have more complex configurations.

# Installation via Docker

LibreChat is published as an image `ghcr.io/danny-avila/librechat` on the GitHub Container Registry. The image comes in two flavours:

* Linux 64-bit AMD
* Linux 64-bit ARM

A few key things to know about the LibreChat container image:

* The container binds to port `3000`.
* The container consists of five services:
  * `api` service (based on image `ghcr.io/danny-avila/librechat-dev`)
  * `mongodb` service (based on image `mongo`)
  * `meilisearch` service (based on image `getmeili/meilisearch`)
  * `vectordb` service (based on image `ankane/pgvector`)
  * `rag_api` service (based on image `ghcr.io/danny-avila/librechat-rag-api-dev-lite`)
* Check out [Docker Compose Override Example][r01] for more configurations.
* Also, you can refer to the [Ubuntu Docker (Remote Linux) Deployment Guide][r02].

[r01]: https://github.com/danny-avila/LibreChat/blob/main/docker-compose.override.yml.example
[r02]: https://www.librechat.ai/docs/remote/docker_linux