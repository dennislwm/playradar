---
authors: dennislwm
title: playscholar
description: An automated process to summarize a web page URL with ChatGPT.
piped_from: Manage_github
piped_to: Make_python
---

# Discussion

Pivot away from Python application to Shell command `curl`.

* Reader API cleans the content of the URL and returns a text URL.
* Python application converts the URL to a PDF, which adds an unnecessary layer.
* Summarizer input requires a text file, which a `curl` command can return from the Reader API.

# Requirements

## Local workstation

* Python 3.12.5 (`/opt/homebrew/bin/python3`)
  * `pdfkit==1.0.0` (`pipenv install`)
  * `pypdf2==3.0.1` (`pipenv install`)

## SaaS accounts

* OpenAI ChatGPT API account
  * `OPENAI_API_KEY`

# Installation and Configuration

## Create an environment, a make and ignore files

1. Create a `app/.env` file.

```sh
export OPENAI_API_KEY=<your-api-key>
```

2. Create a `app/Makefile` file.

```makefile
include .env

.PHONY: check_env test test_verbose venv venv_new venv_run
SHELL := /bin/bash

check_env:
ifndef OPENAI_API_KEY
	$(error Environment variable OPENAI_API_KEY not set)
endif

test: check_env
	@PYTHONPATH=.:../ pytest

test_verbose: check_env
	@PYTHONPATH=.:../ pytest -v -s

venv:
ifndef PIPENV_ACTIVE
	@pipenv shell
else
	@echo -n "Active "
	@pipenv --venv
endif

venv_new:
	@pipenv install --python 3.11
	@pipenv install pdfkit==1.0.0 pypdf2==3.0.1
	@pipenv install --dev pytest==8.3.3

venv_run: check_env venv
	@python --version
```

3. Create a `.gitignore` file.

```sh
.env*
```
