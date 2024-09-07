---
authors: dennislwm
title: pyledger
description: A CLI application to automate debit and credit transactions for Ledger, a double-entry accounting system.
piped_from: pyledger
piped_to: Hypowork
---

# Step 1 - Develop a hypothesis

## Workflow

* [X] Use a testing framework (**pytest**) to automate your testing.
* [X] Use a linter (**check-jsonschema**) to lint the rules YAML file.
* [X] Use a pre-commit hook (**pre-commit**) to lint and test changes on a `git commit`.
* [X] Use a containerization platform (**Docker**) to run your application in any environment.

## Roadmap

* [X] Add `output.amount.prefix` to allow customisable prefix for the output amount, e.g. `$`.
* [X] Add `output.filename` to allow customisable output filename, e.g. `output.txt`.
* [X] Replace any special characters and UPPER case letters within description with spaces and small case letters.
* [ ] Replace `typer` library with `argparse`.

# Step 2 - Rank hypotheses

1. Non-existant viable feature (IT)
  * [X] Use a containerization platform (**Docker**) to run your application in any environment.
  * [X] Add `output.amount.prefix` to allow customisable prefix for the output amount, e.g. `$`.
  * [X] Replace any special characters and UPPER case letters within description with spaces and small case letters.

2. Existing viable feature (CT)
  * [X] Use a testing framework (**pytest**) to automate your testing.
  * [X] Use a linter (**check-jsonschema**) to lint the rules YAML file.

3. Non-existant bloat (IF)
  * [ ] Replace `typer` library with `argparse`.

4. Existing bloat (CF)

# Step 3 - Don't significantly increase complexity

A Minimum Viable Product (MVP) consists of the first two ranked hypotheses:

1. Non-existant viable feature (IT)
2. Existing viable feature (CT)

However, adding the following hypothesis does significantly increase complexity, as `argparse` library is more flexibility but with higher complexity.

4. Non-existant bloat (IF)
  * [ ] Replace `typer` library with `argparse`.

