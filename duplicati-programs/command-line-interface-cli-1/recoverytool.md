---
description: This page describes the Duplicati recovery tool
---

# RecoveryTool

## Duplicati Recovery Tool

This tool performs a recovery of as much data as possible in small steps that must be performed in order. We recommend that you use duplicati-cli to do the restore, and rely only on this tool if all else fails.

The recovery tool is called `Duplicati.CommandLine.RecoveryTool.exe` on Windows and `duplicati-recovery-tool` on Linux and MacOS.

### The steps to perform a disaster recovery are:

1: **Download:** Download files from the remote store and keep them unencrypted on a location available in the local filesystem.

2: **Index:** Builds an index file to figure out what data is contained inside the files downloaded

3: **Restore:** Restores the files to a destination you choose

### Optionally you can also run:

4: **List:** Shows what files are available and tests filters

5: **Recompress:** Ability to change compression type of files on remote backend e.g. from 7z to ZIP

### Download

```
duplicati-recovery-tool download <backend url> <working folder> [options]
```

Downloads all files matching the Duplicati filenames from the remote storage to the current directory, and decrypts them in the process. The remote url must be one supported by Duplicati. Use `duplicati-cli help backends` to see backends and options.

### Index

```
duplicati-recovery-tool index <working folder> [options]
```

Examines all files found in the current folder and produces an `index.txt` file, which is a list of all block hashes found in the files. The index file can be rather large. It defaults to being stored in the current working directory, but can be specified with `--indexfile`. Some files are created in the system temporary folder, use `--tempdir` to set an alternative temporary folder location.

### Restore

```
duplicati-recovery-tool restore <working folder> [version] [options]
```

Restores all files to their respective destinations. Use `--targetpath` to choose another folder where the files are restored into. Use the filters, `--exclude`, to perform a partial restore. Version can be either a number, a filename or a date. If omitted the most recent backup is used.

The restore process requires a fast lookup, which is optimal if all the hashes can be kept in memory. Use the option to `--reduce-memory-use=true` to toggle a slower low-memory restore. If the process is interrupted for any reason, note the file counter and use `--offset=<count>` to start the restore after the last restored file.

Advanced performance options are:&#x20;

* `--reduce-memory-use`: Disables keeping all hashes in memory; use if memory is limited on the restoring machine&#x20;
* `--disable-file-verify`: Disables the initial hashing of the restored file&#x20;
* `--disable-wrapped-zip`: Disable using the faster .NET native ZIP archive in favor of the more resilient one in Duplicati&#x20;
* `--max-open-archives`: Sets the number of archives to keep open for faster access (uses some memory pr. archive); default 200

### List

```
duplicati-recovery-tool list <working folder> [version] [options]
```

Lists contents of backups. Version can be either a number, a filename or a date. If \[version] is omitted a list of backup versions are shown, if \[version] is supplied, files from that version are listed. Use the filters, `--exclude`, to show a subset of files.

### Recompress

```
duplicati-recovery-tool recompress zip <backend url> <working folder> \
  --reupload --reencrypt [options]
```

1. Downloads whole remote storage to the current working folder.
2. Recompress from existing compression type to the chosen compression format.
3. If `--reencrypt` is supplied, again reencrypts using same passphrase (needs to be decrypted for compression type change)
4. If `--reupload` is supplied, files with old compression are deleted and recompressed files are uploaded back to remote storage (it is recommended to take at least temporary copy of remote storage before enabling this switch)

**Warning**: If `--reupload` is supplied it is advisable to specify `--reencrypt` otherwise the files will be uploaded unencrypted!

**Warning**: Before recompress delete the [local database](../../detailed-descriptions/the-local-database.md) and after recompress recreate local database before executing any operation on backup. This allows Duplicati to read new file names from remote storage.

### Supported Options

The backend modules support all their normal options. To see what options a specific backend supports, type:&#x20;

```
duplicati-cli help
```

The environment variables `AUTH_USERNAME` and `AUTH_PASSWORD` are supported. The options `--parameters-file` and `--tempdir` are supported.
