---
authors: dennislwm
title: playscribe
description: An automation tool that summarizes a YouTube link using ChatGPT.
piped_from: 01_ShapeUp
piped_to: RFD
state: committed
---

## Discussion

This project has [expired out-of-the-money](01_ShapeUp.md) due to changes that are [beyond our control](01_ShapeUp#what-happened-to-yt-dlp).

* The workflow of sending a command to Telegram Bot that triggers an automation workflow is broken in the GitHub pipeline.

* The workflow of deploying an image to Docker Hub on a weekly basis is disabled due to a breaking release from [`fabric`](01_ShapeUp#what-happened-to-fabric).

## Shift Right

This project is not `abandoned`, however there are no further plans to add functionality.

* `playscribe` can be executed from your local workstation to [summarize a YouTube link using ChatGPT](https://github.com/dennislwm/playscribe/wiki/Execution#summarize-a-youtube-link-using-chatgpt).

DockerHub and GitHub Container registries contain images for `fabric` v1.4.0. However,  there are no further plans to support newer releases.

* DockerHub Ubuntu-based image: `dennislwm/playscribe:latest`

* GitHub Arm64-based image: `ghcr.io/dennislwm/playscribe:arm64-v1.4.0`
