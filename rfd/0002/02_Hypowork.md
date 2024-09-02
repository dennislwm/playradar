---
authors: dennislwm
title: playpolis
description: An automation workflow for aggregating and displaying markdown files from GitHub, GitLab and local projects.
piped_from: RFD
piped_to: Hypowork
---

# Step 1 - Develop a hypothesis

* [X] Aggregate and display markdown files from GitHub, GitLab and local projects.
  * [X] Automate retrieval of a `wiki` or repo from any GitHub or GitLab project.
  * [X] Manage directories and files while ensuring idempotency in your local workstation.
  * [ ] Ensure that a frontmatter exists with the `publish` attribute in each markdown file.
  * [ ] Publish a folder holding all your Markdown files and images, including all subfolders, to a Markopolis API endpoint.
* [X] Authenticate a user when accessing the published files.
* [X] Allow a user to edit the contents of a publish file and create a new file.

# Step 2 - Rank hypotheses

1. Non-existant viable feature (IT)
  * [X] Aggregate and display markdown files from GitHub, GitLab and local projects.
    * [X] Automate retrieval of a `wiki` or repo from any GitHub or GitLab project.
    * [X] Manage directories and files while ensuring idempotency in your local workstation.
  * [X] Authenticate a user when accessing a SilverBullet server.
2. Existing viable feature (CT)
3. Non-existant bloat (IF)
    * [ ] Ensure that a frontmatter exists with the `publish` attribute in each markdown file.
    * [ ] Publish a folder holding all your Markdown files and images, including all subfolders, to a Markopolis API endpoint.
4. Existing bloat (CF)
  * [X] Allow a user to edit the contents of a publish file and create a new file.

# Step 3 - Don't significantly increase complexity

A Minimum Viable Product (MVP) consists of the first two ranked hypotheses:

1. Non-existant viable feature (IT)
2. Existing viable feature (CT)

Adding the following hypothesis does not significantly increase complexity, as this feature is included in a SilverBullet server.

4. Existing bloat (CF)
  * [X] Allow a user to edit the contents of a publish file and create a new file.

However, adding the following hypotheses increases complexity, due to creating and managing a Markopolis server.

3. Non-existant bloat (IF)
    * [ ] Ensure that a frontmatter exists with the `publish` attribute in each markdown file.
    * [ ] Publish a folder holding all your Markdown files and images, including all subfolders, to a Markopolis API endpoint.