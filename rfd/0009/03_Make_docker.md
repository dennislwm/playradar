---
authors: dennislwm
title: PDF Scan File Size What To Do About It
description: A technique for for compressing PDFs and media files to save space and helps reduce the environmental impact of our digital lives.
piped_from: ADR
piped_to: Make_docker
---

# Requirements

## Local workstation

* `docker`

## SaaS accounts

* OpenAI ChatGPT API account
  * `OPENAI_API_KEY`

# Installation and Configuration

## Create an environment, a make and ignore files

1. Edit `.env` file.

```sh
export DOCKER=docker
export DOCKER_IMAGE=gsmallpdf:arm64
export DOCKER_TOOLS="ghostscript pdftk-java poppler-utils"
```

2. Edit `Makefile` file.

```makefile
-include .env

.PHONY: check_env ci_build docker_clean docker_interactive compress
SHELL := /bin/bash
DOCKER_PS := $(shell $(DOCKER) ps 2> /dev/null)

check_env:
ifndef DOCKER
  $(error Environment variable DOCKER not set)
endif
ifndef DOCKER_IMAGE
  $(error Environment variable DOCKER not set)
endif
ifndef DOCKER_PS
  $(error $(DOCKER) daemon is not running)
endif

ci_build: check_env
  @DOCKER_BUILDKIT=1 $(DOCKER) build --build-arg TOOLS=$(DOCKER_TOOLS) -t $(DOCKER_IMAGE) .

docker_clean: check_env
  @$(DOCKER) image prune -f

docker_interactive: ci_build docker_clean
  @$(DOCKER) run --rm --env-file .env -it --entrypoint bash $(DOCKER_IMAGE)

gsmallpdf: ci_build
  @test $(PDF) || ( echo [Usage] make gsmallpdf PDF=FILE_NAME; exit 1 )
  @$(DOCKER) run --rm -v $(pwd)://home/appuser/app $(DOCKER_IMAGE) $(PDF)
```

3. Create a `.dockerignore` file.

```sh
.env*
```

## Create a Dockerfile

```dockerfile
#----------------------
# From linux base image
FROM ubuntu:latest AS layer_base

#----------------------
# Update system
#   Clean up temp files
#   Create a non-root user
ARG TOOLS=""
FROM layer_base AS layer_user
RUN apt-get update \
    && apt-get install -y --no-install-recommends bash git sudo $TOOLS \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
    && useradd --create-home appuser \
    && echo "appuser ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/appuser \
    && chmod 0440 /etc/sudoers.d/appuser

#----------------------
# Setup user
#   Switch to the non-root user
#   Set up work directory
#   Set file permissions for the app directory
USER appuser
WORKDIR /home/appuser/app
RUN chmod -R 755 /home/appuser/app

#-------------------------------------
# Set default script and arguments
#   This can be overridden at runtime
ENTRYPOINT ["gs", "-sDEVICE=pdfwrite", "-dCompatibilityLevel=1.4", "-dPDFSETTINGS=/ebook", "-dNOPAUSE", "-dQUIET", "-dBATCH", "-sOutputFile=compressed.pdf"]
```
