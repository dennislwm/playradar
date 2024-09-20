---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: Manage_github_action_registry
piped_to: README
state: committed
---

# Introduction

[`playscholar`][r03] is an automated process to summarize a web page URL with ChatGPT.

# Why `playscholar`?

1. This summarization-as-a-service leverages on and applies a similar design from the [`playscribe`][r01] automation to its workflow.

2. This services uses resources in both a free tier and a pay-per-usage tier by cloud SaaS providers:

  |   Free    |      Pay-per-usage      |
  |:---------:|:-----------------------:|
  | Pipedream |         ChatGPT         |
  |  GitHub   | [`jina-ai/reader`][r02] |
  | Telegram  |                         |

3. This service is inexpensive, easy to implement, and can be duplicated for other services.

4. This service does not require implementing a custom frontend as the User will interact with a Telegram Bot.

5. This service does not require a backend server as the processing will be performed using ephemeral runners.

6. This service uses a [distroless image][r04] that drastically reduces the image size, resulting in a faster execution time for its emphemeral runners.

7. Scraping can be complex and often blocked, and raw HTML is cluttered with extraneuous elements like markups and scripts. The Reader API addresses these issues by extracting the core content from a URL and converting it into clean, LLM-friendly text, ensuring high-quality input for your agent and RAG systems.

8. Reader API is multi-modal which means that images on the web page are automatically captioned using a vision language model in the reader and formatted as image alt tags in the output. It natively supports PDF reading and is compatible with most PDFs.

9. Reader API is available for free and offers flexible rate limit and pricing.

# Limitation

This project has several limitations:

* The Telegram Bot has a content limit for messages sent to the User.
* The pipeline job may exceed the time of the fixed delayed step in the workflow.
* The message is truncated after `40` lines and will include a link to the file `examples/result.txt`. However, the content of this file may be overridden by another pipeline.
* The workflow system does not allow conditional branches.
* Reader API has a rate limit of 20 request per minute (rpm) without API key. With an API key, the rate limit increases to 200 rpm.

[r01]: https://github.com/dennislwm/playscribe
[r02]: https://jina.ai/reader
[r03]: https://github.com/dennislwm/playscholar
[r04]: https://github.com/dennislwm/playscholar/pkgs/container/fabric