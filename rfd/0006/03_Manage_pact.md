---
authors: dennislwm
title: pyledger
description: A CLI application to automate debit and credit transactions for Ledger, a double-entry accounting system.
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
        "paisa": {
          "image": "ananthakumaran/paisa",
          "ports": ["7500:7500"],
          "volumes": [
            "/home/dennislwm/data/paisa/:/root/Documents/paisa/"
          ]
        }
      }
    }
  },
  "templates":[
    "service-pact/portainer-stack/docker-compose.j2"
  ],
  "output": {
    "file_suffix": "-paisa"
  }
}
```
