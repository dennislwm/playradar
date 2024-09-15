---
authors: dennislwm
title: PDF Scan File Size What To Do About It
description: A technique for for compressing PDFs and media files to save space and helps reduce the environmental impact of our digital lives.
piped_from: Playradar
piped_to: ADR
status: proposed
date: 2024-09-15
---

# AD: PDF Scan File Size What To Do About It

## Problem Statement

How do we reduce a PDF file size while ensuring high quality and privacy?

## Context

* There are many online PDF tools that are convenient and easy to use, such as [Smallpdf.com][r01]. However, these tools are free to use but with certain limitations, such as a rate limit of 2 files per seven hours.
* There is no privacy because you will need to upload your PDFs onto their server in order to process them.
* The ratio of compression and its quality may vary depending on which services that you use.
* There may be other self-hosted tools that have multiple features, but we don't need to host an application that has so many features.

## Decision Driver

1. Prefer privacy over cloud services.
2. Prefer free over paid services.
3. Prefer a CLI application over a web application.
4. Prefer high quality and compression ratio.

The above drivers are listed and ranked based on priority.

## Considered Option

1. `Privacy`: Sensitive documents should not be shared or uploaded to a server.
    * `gs` can be published as a Docker image and run as a container on your workstation.
2. `Free`: Free or inexpensive option with a reasonable rate limit.
    * `gs` is a free tool that can be installed on any Linux OS and used with no limit.
3. `CLI`: a CLI application that can easily be integrated with any scripts, such as [aklomp/shrinkpdf][r02].
    * `gs` is a CLI tool that can be published as a Dockerfile with your own custom script.
4. `Compression ratio`: a higher Compression Ratio (CR) indicates a smaller compressed file size and higher space savings.
    * Smallpdf achieves a higher compression ratio, without any discernable difference in quality.
    * On average, Smallpdf achieves a compression ratio of 0.78 (CR1), while `gs` has a lower average of 0.38 (CR2).
    * `gs` compression ratio is approximately half of Smallpdf.


    |                Document                 |  Size  | Smallpdf | CR1  |  `gs`  | CR2  | CR1 / CR2 |
    |:---------------------------------------:|:------:|:--------:|:----:|:------:|:----:|:---------:|
    |  129-data-structures-athanassoulis.pdf  | 3.4 MB |   1 MB   | 0.71 | 2.3 MB | 0.32 |    45%    |
    | 139-The Misfit Who Built the IBM PC.pdf | 4.5 MB |  617 KB  | 0.86 | 2.7 MB | 0.40 |    47%    |
    |      151-How to friction logs.pdf       | 876 KB |  211 KB  | 0.76 | 419 KB | 0.42 |    55%    |

## Decision Outcome

* `gs` has a higher decision score than Smallpdf.

|   Tool   | Score | Privacy | Free | CLI | Compression ratio | Quality |
|:--------:|:-----:|:-------:|:----:|:---:|:-----------------:|:-------:|
|   `gs`   |   4   |    1    |  1   |  1  |         0         |    1    |
| Smallpdf |   1   |    0    |  0   |  0  |         1         |    1    |


## Consequences

[r01]: https://smallpdf.com
[r02]: https://github.com/aklomp/shrinkpdf
