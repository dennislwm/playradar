---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: Make_python
piped_to: Make_shell
---

# Discussion

Pivot away from Python application to Shell command `curl`.

* Reader API cleans the content of the URL and returns a text URL.
* Python application converts the URL to a PDF, which adds an unnecessary layer.
* Summarizer input requires a text file, which a `curl` command can return from the Reader API.

# Requirements

## Local workstation

* `curl` 8.7.1 (`/usr/bin/curl`)
* `docker` 27.1.1 (`/opt/homebrew/bin/docker`)

## SaaS accounts

* OpenAI ChatGPT API account
  * `OPENAI_API_KEY`

# Installation and Configuration

## Create an environment, a make and ignore files

1. Create a `app/.env` file.

```sh
export OPENAI_API_KEY=<your-api-key>
export DOCKER=docker
export DOCKER_IMAGE=playscribe
```

2. Create a `app/Makefile` file.

```makefile
include .env

.PHONY: check_env scholar
SHELL := /bin/bash
DOCKER_PS := $(shell $(DOCKER) ps 2> /dev/null)

check_env:
ifndef OPENAI_API_KEY
	$(error Environment variable OPENAI_API_KEY not set)
endif
ifndef DOCKER
	$(error Environment variable DOCKER not set)
endif
ifndef DOCKER_IMAGE
	$(error Environment variable DOCKER_IMAGE not set)
endif
ifndef DOCKER_PS
	$(error $(DOCKER) daemon is not running)
endif

scholar: check_env
	@test $(URL) || ( echo [Usage] make scholar URL=WEB_PAGE_LINK; exit 1 )
	@source ./make.sh && get_output $(URL)
	@cat output.txt | $(DOCKER) run --rm --env-file .env -i $(DOCKER_IMAGE) --pattern extract_article_wisdom && rm output.txt
```

3. Create a `.gitignore` file.

```sh
.env*
```
