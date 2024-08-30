---
authors: dennislwm
title: [SilverBullet](https://silverbullet.md/SilverBullet)
description: A note-taking web application stored as Markdown files.
piped_from: [Playradar](./01_Playradar.md)
piped_to: [Manage-pact](https://gitlab.com/dso-coe/manage-pact)
---

# Introduction

[Manage-pact](https://gitlab.com/dso-coe/manage-pact) is a GitLab operational pipeline that abstracts away the specifics of creating a Portainer stack. It uses a Jinja template to convert an input JSON file into an output YAML file.

There are several [benefits and values](https://gitlab.com/dso-coe/manage-pact#21-benefits-and-values) from using `manage-pact` as an automation pipeline.

# Installation and Configuration

The runbook [Creating a config JSON file for Portainer stack](https://gitlab.com/dso-coe/manage-pact#61-creating-a-config-json-file-for-portainer-stack) should be performed by the DevSecOps Engineer.

```json
{
  "data": {
    "variables": {
      "services": {
        "silverbullet": {
          "image": "zefhemel/silverbullet",
          "ports": ["8090:3000"],
          "environment": ["SB_USER=$SB_USER", "SB_SHELL_BACKEND=off", "SB_SPACE_SCRIPT=off"],
          "volumes": [
            "silverbullet_vol_space:/space"
          ]
        }
      },
      "volumes": ["vol_space"]
    }
  },
  "templates":[
    "service-pact/portainer-stack/docker-compose.j2"
  ],
  "output": {
    "file_suffix": "-silverbullet"
  }
}
```

If the `build` stage passes, an `install` stage will create a merge request link for the project [service-pact](https://gitlab.com/dso-coe/service-pact) that requires a manual step.

## Test a YAML file by deploying a Portainer stack

1. Navigate to your Portainer console > Stacks > **Add stack**.

2. Copy and paste a YAML file from the above MR and assign a stack name, e.g. `silverbullet`.

```yml
version: '2'

services:

  silverbullet:

    image: zefhemel/silverbullet

    restart: unless-stopped
    container_name: objSilverbullet
    ports:
      - 8090:3000
    environment:
      - SB_USER=$SB_USER
      - SB_SHELL_BACKEND=off
      - SB_SPACE_SCRIPT=off
    volumes:
      - vol_space:/space

volumes:
  vol_space:
```

3. Add any sensitive environment variables using the Portainer UI.
  - `SB_USER`: `admin:password`

4. Click **Deploy the stack** and wait for it to be deployed.
