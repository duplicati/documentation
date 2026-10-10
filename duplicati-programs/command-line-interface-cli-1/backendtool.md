---
description: This page describes the backend tool in Duplicati
---

# BackendTool

The BackendTool is intended to provide commandline access to the remote destination. This can be used to create remote folders, locate remote files, and fetch remote files.

The BackendTool is called `Duplicati.CommandLine.BackendTool.exe` on Windows and `duplicati-backend-tool` on Linux and MacOS.

The basic usage for the tool is:

```
duplicati-backend-tool <command> <remote url> [filename] [lock-duration]
```

There are 7 supported commands: GET, PUT, DELETE, LIST, CREATEFOLDER, GET-LOCK, and SET-LOCK.

The GET, PUT, and DELETE commands will download, upload, and delete a file, respectively. The filename parameter refers to the remote filename and will be matched to a local filename. It is not possible to have different filenames on the remote and local system with this operation. Note that any change to the remote storage will likely require a recreate of the [local database](../../detailed-descriptions/database-and-storage/the-local-database.md).

The LIST command will simply list all files found on the remote location and has no side-effects. The CREATEFOLDER command can be used to create folders in preparation for making a backup or moving files.

The GET-LOCK and SET-LOCK commands work with [remote file locking](../../detailed-descriptions/security-and-secrets/using-remote-file-locking.md) on destinations that support it. GET-LOCK prints the lock expiration of a remote file in ISO 8601 UTC, or `null` if the file has no lock. SET-LOCK locks a remote file for the given duration:

```
duplicati-backend-tool GET-LOCK <remote url> <filename>
duplicati-backend-tool SET-LOCK <remote url> <filename> 30D
```
