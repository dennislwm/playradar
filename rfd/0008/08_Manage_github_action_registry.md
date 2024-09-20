---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: Make_docker_go
piped_to: Manage_github_action_registry
---

# Installation and Configuration

## Configure CI variables for GitHub Actions

This runbook should be performed by the DevSecOps.

1. Open a web browser > Navigate to the GitHub project > Settings > Secrets and variables > Actions > Secrets > Click **New repository secret**.
  * `OPENAI_API_KEY`: Your ChatGPT API key.
  * `REGISTRY_USER`: Your Container Registry Username.
  * `REGISTRY_PASS`: Your Container Registry Password / Personal Access Token.

2. Select Variables > Click **New repository variable**
  * `REGISTRY_NAME`: Your Container Registry name, e.g. `ghrc.io`.
  * `DOCKER`: `docker`
  * `DOCKER_IMAGE`: `ghcr.io/dennislwm/fabric`
  * `DOCKER_TAG_VERSION`: `amd64-v1.4.21`

## Create a GitHub CI file

This runbook should be performed by the DevSecOps.

1. Create a file `.github/workflows/registry.yml`

```yml
name: Publish Registry
#+-----------------------------------------------------------------------------------------+
#|                            E X T E R N A L   T R I G G E R S                            |
#+-----------------------------------------------------------------------------------------+
# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
# * * * * *  UTC (Convert to Singapore: +0800)
# ┬ ┬ ┬ ┬ ┬
# │ │ │ │ └───── day of week (0 - 7) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
# │ │ │ └────────── month (1 - 12)
# │ │ └─────────────── day of month (1 - 31)
# │ └──────────────────── hour (0 - 23)
# └───────────────────────── min (0 - 59)
on:
  push:
    branches: [ main ]
    paths: [ Dockerfile, .github/workflows/registry.yml ]
  # schedule: [ cron:  '1 0 * * 5' ]

jobs:
  build:

    runs-on: ubuntu-latest
    env:
      OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
      REGISTRY_USER: ${{ secrets.REGISTRY_USER }}
      REGISTRY_PASS: ${{ secrets.REGISTRY_PASS }}
      REGISTRY_NAME: ${{ vars.REGISTRY_NAME }}
      DOCKER: ${{ vars.DOCKER }}
      DOCKER_IMAGE: ${{ vars.DOCKER_IMAGE }}
      DOCKER_TAG_VERSION: ${{ vars.DOCKER_TAG_VERSION }}
    steps:
    - uses: actions/checkout@v2
      with:
        fetch-depth: 0
    - name: Check Environment
      run: |
        if [ -z $OPENAI_API_KEY ] || [ -z $REGISTRY_USER ] || [ -z $REGISTRY_PASS ] || [ -z $REGISTRY_NAME ] || [ -z $DOCKER ] || [ -z $DOCKER_IMAGE ] || [ -z $DOCKER_TAG_VERSION ]; then
          echo [ERROR] Missing one of CI variables [OPENAI_API_KEY, REGISTRY_USER, REGISTRY_PASS, REGISTRY_NAME, DOCKER, DOCKER_IMAGE, DOCKER_TAG_VERSION].
          exit 1
        fi
    - name: Check Registry
      run: |
        echo "$REGISTRY_PASS" | $DOCKER login $REGISTRY_NAME -u "$REGISTRY_USER" --password-stdin
        found=$( $DOCKER manifest inspect $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION > /dev/null && echo 1 || echo 0 )
        if [ $found -eq 1 ]; then
          echo [ERROR] Duplicate image $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION in registry.
          exit 1
        fi
    - name: Build Docker Image
      run: |
        DOCKER_BUILDKIT=1 $DOCKER build -t $DOCKER_IMAGE:latest .
        $DOCKER run --rm --env OPENAI_API_KEY=$OPENAI_API_KEY -i $DOCKER_IMAGE:latest --listmodels
        $DOCKER tag $DOCKER_IMAGE:latest $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION
        $DOCKER image ls
    - uses: trstringer/manual-approval@v1
      timeout-minutes: 5
      with:
        secret: ${{ github.TOKEN }}
        approvers: dennislwm
        minimum-approvals: 1
        issue-title: "Publish docker image to registry"
        issue-body: "Please approve or deny the deployment."
    - name: Publish Registry
      run: |
        $DOCKER push $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION
    - name: Validate Registry
      run: |
        $DOCKER image rm $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION
        $DOCKER run --rm --env OPENAI_API_KEY=$OPENAI_API_KEY -i $REGISTRY_NAME/$REGISTRY_USER/$DOCKER_IMAGE:$DOCKER_TAG_VERSION --listmodels
```
