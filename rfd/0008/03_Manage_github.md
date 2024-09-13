---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: RFD
piped_to: Manage_github
---

# Installation and Configuration

The runbook [Create a new project in GitHub][r01] should be performed by the DevSecOps Engineer.

[r01]: https://gitlab.com/dso-coe/manage-github/-/wikis/Execution#create-a-new-project-in-github

1. Edit the `repo_gitver` variable.

```diff
variable "repo_gitver" {
  type = map(object({
    name        = string
    description = string
  }))
  default = {
    ...
+   "playscholar" = {
+     name        = "playscholar",
+     description = "An automated process to summarize a web page URL with ChatGPT."
+   },
  }
}
```

2. Run `git` add, commit and push to the main branch to trigger a pipeline.
