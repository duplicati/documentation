---
description: >-
  This page describes the SyncTool command line tool for mirroring a backup to
  another backend.
---

# SyncTool

## Description

This tool synchronizes two remote backends. The tool assumes that the intent is to have the destination match the source.

If the destination has files that are not in the source, they will be deleted (or renamed if the `--retention` option is set).

If the destination has files that are also present in the source, but the files differ in size, or if the source files have a newer (more recent) timestamp, the destination files will be overwritten by the source files. Given that some backends do not allow for metadata or timestamp modification, and that the tool is run after backup, the destination files should always have a timestamp that is newer (or the same if run promptly) compared to the source files.

If the `--force` option is set, the destination will be overwritten by the source, regardless of the state of the files. It will also skip the initial comparison, and delete (or rename) all files in the destination.

If the `--verify` option is set, the files will be downloaded and compared after uploading to ensure that the files are correct. Files that already exist in the destination will be verified before being overwritten (if they seemingly match).

## Usage

```
Duplicati.CommandLine.SyncTool <backend_src> <backend_dst> [options]
```

## Arguments

* `backend_src` : The source backend string.&#x20;
* `backend_dst` : The destination backend string

## Options

* `-y, --confirm, --yes` : Automatically confirm the operation. Default is `false`.
* `-d, --dry-run` : Do not actually write or delete files. If not set here, the global options will be checked. Default is `false`.&#x20;
* `--dst-options` : Options for the destination backend. Each option is a key-value pair separated by an equals sign, e.g. `--dst-options key1=value1 key2=value2` . Default is an empty collection of options.
* `-f, --force` : Force the synchronization, overwriting all of the destination files regardless of their state. Default is `false`.
* `--global-options` : Global options all backends. May be overridden by backend specific options (`--src-options`, `--dst-options`). Each option is a key-value pair separated by an equals sign, e.g. `--global-options key1=value1 key2=value2` . Default is an empty collection of options.
* `--log-file` : The log file to write to. If not set here, global options will be checked. Default is an empty collection of options.
* `--log-level` : The log level to use. If not set here, global options will be checked Default is `information`.
* `--parse-arguments-only` : Only try to parse the arguments and then exit. Default is `false`.
* `--progress` : Print progress to STDOUT. Default is `false`.
* `--retention` : Toggles whether to keep old files. Any deletes will be renamed instead. Default is `false`.
* `--retry` : Number of times to retry on errors. Default is `3`.
* `--src-options` : Options for the source backend. Each option is a key-value pair separated by an equals sign, e.g. `--src-options key1=value1 key2=value2` . Default is an empty collection of options.&#x20;
* `--verify-contents` : Verify the contents of the files to decide whether the pre-existing destination files should be overwritten. Default is `false`.
* `--verify-get-after-put` : Verify the files (by downloading and comparing) after uploading them to ensure that they were uploaded correctly. Default is `false`.&#x20;
* `--version` : Print the version information and exit.
* `-?, -h, --help` : Show help and usage information.
