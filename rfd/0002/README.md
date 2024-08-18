---
publish: false
state: prediscussion
authors: dennislwm
title: Markopolis
description: A web app and API server designed to server Markdown files.
labels: self-host, api, markdown, web-app
---

# Introduction

Markopolis is a web app and API server designed to server Markdown files.

* Allows you to share Markdown notes as websites and interact with and manipulate your Markdown files using an API.

* Run a CLI in your local workstation and point in to a directory holding all your Markdown files and images, and the library takes care of everything else.

## Why Markopolis?

1. Currently, there is no centralised method for aggregating and displaying markdown files from GitHub, GitLab and local projects. This creates a document sprawl and it becomes unwieldy and inefficient to manage playbooks, runbooks, and other documents.

2. Markopolis provides an automated way to publish and share markdown notes as a web site. A self-hosted site with a public access through Cloudflare reverse proxy and SSO provides additional safeguards for sensitive notes.

3. A `git-sync` tool can be used to automate retrieval of a `wiki` from any GitHub or GitLab project.

4. A Markopolis CLI can be used to automate publishing a directory holding all your Markdown files and images.

5. A `pyinfra` CLI or `bash` script can be used to orchestrate multiple directories in your local workstation.