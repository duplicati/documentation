---
description: This page describes the SourceTool for inspecting remote sources
---

# SourceTool

The SourceTool lists or downloads the files that a remote source exposes. It is useful for checking that a remote source URL works, and for seeing what Duplicati would back up from it, before using it in a backup.

The SourceTool is called `Duplicati.CommandLine.SourceTool.exe` on Windows and `duplicati-source-tool` on Linux and MacOS.

The URL can be any source provider, such as the [Microsoft 365](../../detailed-descriptions/automation-and-integration/office-365-backup-and-restore.md) or [Google Workspace](../../detailed-descriptions/automation-and-integration/google-workspace-backup-and-restore.md) sources, or a [destination](../../backup-destinations/destination-overview.md) that supports browsing folders. In 2.4 these are the File, SSH, SMB, S3, IDrive e2, Box, Dropbox, Google Drive, and Microsoft Graph based (OneDrive, SharePoint, Microsoft Group) destinations.

## List

To list the files and folders of a remote source, run:

```
duplicati-source-tool list <url>
```

By default the whole tree is listed. Use `--max-depth=<n>` to limit how many folder levels are visited; `0` means no limit.

## Download

To download the files of a remote source to a local folder, run:

```
duplicati-source-tool download <url> --destination=<local folder>
```

If `--destination` is not given, the files are downloaded to the current folder. The options are:

* `--max-depth`: The number of folder levels to visit. `0` (the default) means no limit.
* `--max-size`: Skip files larger than this size in bytes. `0` (the default) means no limit.
* `--overwrite`: Overwrite files that already exist in the destination folder.
