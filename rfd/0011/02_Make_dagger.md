---
authors: dennislwm
title: dagger.io
description: An API that helps you create a consistent, reliable development and testing environment on your local host and ensure that everything your CI can do, your local environment can do too.
piped_from: Playradar
piped_to: Make_dagger
---

# Requirements

## Local workstation

* Dagger v0.13.6 (`/opt/homebrew/bin/dagger`)

# Installation and Configuration

## Create an environment, a make and ignore files

1. Create a `app/.env` file.

```sh
export OPENAI_API_KEY=<your-api-key>
```

2. Create a `app/Makefile` file.

```makefile
-include .env

.PHONY: check_env dagger_init dagger_list dagger_grep
SHELL := /bin/bash
DOCKER_PS := $(shell $(DOCKER) ps 2> /dev/null)

check_env:
ifndef DOCKER
  $(error Environment variable DOCKER not set)
endif
ifndef DOCKER_PS
  $(error $(DOCKER) daemon is not running)
endif

dagger_init: check_env
  dagger init --sdk=python --source=./dagger

dagger_list: check_env
  dagger functions

dagger_grep: check_env
  @test $(STR) || ( echo [Usage] make dagger_grep STR=PATTERN; exit 1 )
  dagger call grep-dir --directory-arg . --pattern $(STR)
```