# Files for Pi-Apps

This repository contains files for pi-apps, acting as a file storage and a safe place for them without tampering.

## Maintainers

Please add the following script to `./.git/hooks/pre-commit`

```shell
#! /bin/bash

sha256sum ./* > sha256sums.txt
git add sha256sums.txt

```

This script will update the sha256sums for all of the files, so you can validate any files downloaded.
