---
authors: dennislwm
title: AnythingLLM
description: A full-stack application that enables you to turn any document, resource, or piece of content into context that any LLM can use as references during chatting.
piped_from: Playradar
piped_to: Manage_Pact
---

# Installation and Configuration

The runbook [Creating a config JSON file for Portainer stack](https://gitlab.com/dso-coe/manage-pact#61-creating-a-config-json-file-for-portainer-stack) should be performed by the DevSecOps Engineer.

```json
{
  "data": {
    "variables": {
      "services": {
        "anythingllm": {
          "image": "mintplexlabs/anythingllm",
          "ports": ["3001:3001"],
          "environment": ["STORAGE_DIR=/app/server/storage"],
          "volumes": [
            "/home/dennislwm/data/anythingllm/:/app/server/storage",
            "/home/dennislwm/data/anythingllm/.env:/app/server/.env"
          ]
        }
      }
    }
  },
  "templates":[
    "service-pact/portainer-stack/docker-compose.j2"
  ],
  "output": {
    "file_suffix": "-anythingllm"
  }
}
```
