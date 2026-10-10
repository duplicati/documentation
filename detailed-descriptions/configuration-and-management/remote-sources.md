---
description: This page describes how to back up files from a remote location
---

# Remote sources

{% hint style="info" %}
Remote sources can be added in the user interface from Duplicati 2.3.0.0.
{% endhint %}

Besides local files and folders, a backup can include files from a remote location, such as an SFTP server, an S3 bucket, or a cloud drive. Duplicati reads the files directly from the remote location during the backup, so they do not need to be copied to the local machine first.

Remote sources are also used for the [Microsoft 365](../automation-and-integration/office-365-backup-and-restore.md) and [Google Workspace](../automation-and-integration/google-workspace-backup-and-restore.md) backups.

## Supported remote sources

In Duplicati 2.4 the following [destinations](../../backup-destinations/destination-overview.md) can also be used as remote sources: File, SFTP (SSH), SMB, S3, IDrive e2, Box, Dropbox, Google Drive, and the Microsoft Graph based destinations (OneDrive, SharePoint, Microsoft Group).

## Adding a remote source in the UI

On the "Source Data" step of the backup setup, click "Add remote path". Pick the type of remote source and fill in the connection details as you would for a destination, then test the connection and click "Use remote source". The button is only enabled after the connection has been tested.

The remote source is shown in the source tree like a folder, and you can select and filter its contents like local files.

## Adding a remote source from the commandline

On the commandline, a remote source is given as a source path in the format `@<mount point>|<url>`:

```
duplicati-cli backup <destination url> "@/remote/server1|ssh://server1/data?auth-username=..." /home/user/documents
```

The mount point is a rooted path (such as `/remote/server1` or `C:\remote\server1`) that decides where the remote files appear in the backup. The files from the example above are stored as `/remote/server1/...`, and that is also the path you will see when [restoring](../../getting-started/restoring-files.md). The url is a regular destination url, including any options it needs.

The [SourceTool](../../duplicati-programs/command-line-interface-cli-1/sourcetool.md) can be used to check a remote source url before using it in a backup.

{% hint style="warning" %}
If a remote source cannot be reached during a backup, Duplicati logs a warning and completes the backup without the files from that source. If the source stays unreachable, retention rules can eventually delete the last version that still has its files. Keep an eye on warnings for backups with remote sources.
{% endhint %}
