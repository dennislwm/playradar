---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: Playradar
piped_to: Make_docker_go
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

```diff
+export OPENAI_API_KEY=<your-openai-api-key>
export DOCKER=docker
+export DOCKER_IMAGE=fabric
+export DOCKER_TAG_VERSION=arm64-v1.4.21
```

2. Edit `Makefile` file.

```diff
...
-.PHONY: check_env ci_build docker_clean docker_interactive
+.PHONY: check_env ci_build docker_clean docker_interactive scholar
...

check_env:
...
+ifndef OPENAI_API_KEY
+ $(error Environment variable OPENAI_API_KEY not set)
+endif
+ifndef DOCKER_TAG_VERSION
+ $(error Environment variable DOCKER_TAG_VERSION not set)
+endif

ci_build: check_env
 @DOCKER_BUILDKIT=1 $(DOCKER) build -t $(DOCKER_IMAGE):latest .
+ $(DOCKER) tag $(DOCKER_IMAGE):latest ghcr.io/dennislwm/$(DOCKER_IMAGE):$(DOCKER_TAG_VERSION)

...
+scholar: check_env
+ @test $(URL) || ( echo [Usage] make scholar URL=WEB_PAGE_LINK; exit 1 )
+ @source ./make.sh && get_output $(URL)
+ @cat output.txt | $(DOCKER) run --rm --env-file .env -i $(DOCKER_IMAGE) --pattern extract_article_wisdom && rm output.txt
```

## Create a Dockerfile

```dockerfile
LABEL org.opencontainers.image.source https://github.com/dennislwm/playscholar

# Start by building the application.
FROM golang:1.23 as build

#----------------------
# Install fabric
#   Clone git repo
#   Install fabric
RUN go install github.com/danielmiessler/fabric@latest
RUN mkdir -p /root/.config/fabric && \
  echo DEFAULT_MODEL=gpt-4o-mini > /root/.config/fabric/.env && \
  echo DEFAULT_VENDOR=OpenAI >> /root/.config/fabric/.env && \
  echo PATTERNS_LOADER_GIT_REPO_URL=https://github.com/danielmiessler/fabric.git >> /root/.config/fabric/.env && \
  echo PATTERNS_LOADER_GIT_REPO_PATTERNS_FOLDER=patterns >> /root/.config/fabric/.env && \
  echo OPENAI_API_BASE_URL=https://api.openai.com/v1 >> /root/.config/fabric/.env
RUN fabric --updatepatterns

# # Now copy it into our base image.
FROM gcr.io/distroless/base
COPY --from=build /go/bin/fabric /
COPY --from=build /root/.config/fabric /root/.config/fabric

ENTRYPOINT ["/fabric"]
```
