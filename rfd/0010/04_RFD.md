---
authors: dennislwm
title: PlayPdf
description: A commandline utility to add an OCR text layer to scanned PDF files, allowing them to be searched or copy-pasted.
piped_from: Playradar
piped_to: RFD
---

# Discussion

Pivot away from [`phiresky/ripgrep-all`][r01] to [`pdfgrep`][r02].

* `ripgrep-all` has more features, such as searching `zip` etc, however these features are not required and adds more bloat to the project.
* `ripgrep-all` installation requires **MacPorts**, which in turn requires **Xcode**.
* `ripgrep-all` file size (>7 MB) is larger than `pdfgrep` file size (~0.15 MB).
* `pdfgrep` has one main feature, which is searching `pdf` files.
* `pdfgrep` installation uses `brew install`.

[r01]: https://github.com/phiresky/ripgrep-all
[r02]: https://pdfgrep.org

# `playpdf` Application

* The `playpdf` application has several folders that is required for its operations:
  * `SRC`: A string that specifies a source folder, e.g. `/Volumes/home/Scan`.
    * Read method only during an OCR operation.
  * `TMP`: A string that specifies a temporary folder, e.g. `./tmp`.
    * Create method during an OCR operation.
    * Read and delete methods during a search operation.
  * `TRU`: A string that specifies a destination folder, e.g. `~/ScanOcr`.
    * Create method only during a search operation.
* The source of truth for all operations is a `TRU` folder.
  * If a file exists in `TRU`, i.e. `<FILE>.<PRE>.pdf`, then no further operations will be performed for `<FILE>.pdf` in either `SRC` or `TMP` folders.
* In order to improve performance for an OCR operation, if a file exists in `TMP`, i.e. `<FILE>.pdf`, then no OCR operation will be performed for that file.
* The operations below are idempotent, as performing it multiple times will not change its outcome.
  * **OCR** (`ocrmypdf`): An operation that results in a searchable file from an input file.
    * During an OCR operation, a file from `SRC` will result in a searchable file in `TMP` with the same name, i.e. `<FILE>.pdf`.
    * A `SRC` folder acts as an input folder for an OCR operation, and files will be read, but never updated or deleted.
    * A `TMP` folder acts as a temporary folder for an OCR operation, and searchable files will be created.
    * A `TMP` folder also acts as an input folder for a search operation, and files will be read and deleted (moved to a destination folder).
  * **Search** (`pdfgrep`): An operation that moves a searchable file to a source-of-truth folder.
    * During a search operation, if a `TEXT` is found within `<FILE>.pdf` in `TMP` folder, it will be moved to `TRU` folder, and renamed `<FILE>.<PRE>.pdf`.
    * A `TRU` folder acts mainly as a source of truth folder for both OCR and search operations, as well as a destination folder for a search operation.
* The operation below is for housekeeping only.
  * **Purge** (`rm`): An operation that deletes all searchable files in `TMP` folder.

# Requirements

## Local workstation

* `ocrmypdf` v16.5.0 (`/opt/homebrew/bin/ocrmypdf`)
* `pdfgrep` v2.2.0 (`/opt/homebrew/bin/pdfgrep`)

# Installation and Configuration

## Create a makefile

1. Create a `Makefile` file.

```makefile
-include .env

TRU := ~/ScanOcr
PDF := *.pdf
TMP := ./tmp
SRC := /Volumes/home/Scan
TRU_COUNT := $(shell if ls $(TRU)/$(PDF) 1> /dev/null 2>&1; then ls $(TRU)/$(PDF) | wc -l; else echo 0; fi)
TRU_EXIST := $(shell if [ -d $(TRU) ]; then echo 1; else echo 0; fi )
PDF_COUNT := $(shell if ls $(SRC)/$(PDF) 1> /dev/null 2>&1; then ls $(SRC)/$(PDF) | wc -l; else echo 0; fi)
PDF_MANIFEST := $(shell if ls $(SRC)/$(PDF) 1> /dev/null 2>&1; then echo 1; else echo 0; fi)
TMP_EXIST := $(shell if [ -d $(TMP) ]; then echo 1; else echo 0; fi )
TMP_MANIFEST := $(shell ls $(TMP)/$(PDF))
SRC_EXIST := $(shell if [ -d $(SRC) ]; then echo 1; else echo 0; fi )
SRC_MANIFEST := $(shell ls $(SRC)/$(PDF))

.PHONY: check_io magic
SHELL := /bin/bash

check_io:
  @if [ $(TRU_EXIST) -eq 0 ]; then echo "[Error] $(TRU) not mounted."; exit 1; fi
  @if [ $(TMP_EXIST) -eq 0 ]; then echo "[Error] $(TMP) not mounted."; exit 1; fi
  @if [ $(SRC_EXIST) -eq 0 ]; then echo "[Error] $(SRC) not mounted."; exit 1; fi
  @if [ $(PDF_MANIFEST) -eq 0 ]; then echo "[Error] $(SRC) no PDF file."; exit 1; fi
  @echo Input: $(SRC) contains $(PDF_COUNT) PDF files.
  @echo Output: $(TRU) contains $(TRU_COUNT) PDF files.

ocr_cache: check_io
  @for f in $(SRC_MANIFEST); do\
    FILE=$$(basename "$$f" ".pdf").pdf;\
    BASE=$$(basename "$$f" ".pdf");\
    TRU_MANIFEST=$$(if ls $(TRU)/$$BASE* 1> /dev/null 2>&1; then echo 1; else echo 0; fi);\
    if [ $$TRU_MANIFEST -eq 0 ]; then\
      FILE_CACHE=$$(if ls $(TMP)/$$FILE 1> /dev/null 2>&1; then echo 1; else echo 0; fi);\
      if [ $$FILE_CACHE -eq 0 ]; then\
        $$(TRUmypdf --pages 1 $(SRC)/$$FILE $(TMP)/$$FILE);\
      fi;\
    fi;\
  done

ocr_prune: check_io
  @rm $(TMP)/$(PDF)\

magic: ocr_cache
  @test "$(TEXT)" || ( echo [Usage] make magic TEXT=SEARCH_TEXT [PRE=FILE_PREFIX]; exit 1 )
  @if [ -z $(PRE) ]; then\
    pdfgrep "$(TEXT)" $(TMP)/$(PDF);\
  else\
    for f in $(TMP_MANIFEST); do\
      FILE=$$(basename "$$f" ".pdf").pdf;\
      BASE=$$(basename "$$f" ".pdf");\
      TRU_MANIFEST=$$(if ls $(TRU)/$$BASE* 1> /dev/null 2>&1; then echo 1; else echo 0; fi);\
      if [ $$TRU_MANIFEST -eq 0 ]; then\
        GREP=$$(pdfgrep "$(TEXT)" $(TMP)/$$FILE);\
        if [ -n "$$GREP" ]; then\
          echo mv "$(TMP)/$$FILE" "$(TRU)/$$BASE.$(PRE).pdf";\
          $$(eval mv "$(TMP)/$$FILE" "$(TRU)/$$BASE.$(PRE).pdf");\
        fi;\
      fi;\
    done;\
  fi;\
  echo Done.
```
