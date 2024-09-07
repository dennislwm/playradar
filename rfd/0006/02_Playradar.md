---
authors: dennislwm
title: pyledger
description: A CLI application to automate debit and credit transactions for Ledger, a double-entry accounting system.
piped_from: Hypowork
piped_to: Playradar
quadrant: platforms
rings: trial
org_size: micro
---

# Introduction

Paisa is a Personal finance manager build on top of the ledger double entry accounting tool.

1. Track the latest market price of your Mutual Fund, NPS Fund and Stock holdings.
2. Track and budget your Expenses.
3. Convert CSV, Excel and PDF files to Ledger journal.
4. Calculate your taxes, emis, etc using sheets.
5. View your recurring transactions and credit card bills.
6. Compatible with `ledger`, `hledger` and `beancount`.

# Installation Options

You have a few options here:

1. Installation via [Desktop Application][r01] for Linux, Mac, and Windows.
2. Installation via Docker (which is covered in this document).

## Installation via Docker

Paisa is published as a docker image on [DockerHub][r02]. The image comes in two flavours:

* Linux Intel
* Linux ARM

A few key things to know about the container image:

* The container binds to port `7500`.
* The container uses `/root/Documents/paisa/` as the root folder. You can connect a docker volume, or a host folder to this.
* The container does not support authentication.
* The container comes with an embedded `ledger` binary and will use it if it's not already installed on your system.
* Check out [Configuration][r03] for more interesting environment variables you can set.

[r01]: https://github.com/ananthakumaran/paisa/releases/latest
[r02]: https://hub.docker.com/r/ananthakumaran/paisa
[r03]: https://paisa.fyi/reference/config/

## Workflow

This project uses several methods and products to optimize your workflow.

* [ ] Use a desktop application (**Paisa**) to validate `ledger` accounts.
* [ ] Use a web application (**Paisa**) to share `ledger` reports.