---
description: >-
  This page describes how to use the file destination provider to store backup
  data on a local drive.
---

# File Destination

The most basic destination in Duplicati is the file backend. This backend simply stores the backup data somewhere that is reachable from the file system. The destination can be a network based storage as long as it is mounted when needed, a fixed disk, or a removable media.

{% hint style="info" %}
Note that for Windows network shares, you may want to use the [CIFS/SMB destination](cifs-aka-smb-destination.md) instead.
{% endhint %}

## User interface

In the user interface you simply need to either pick or type the path to where the backup data will be stored.

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.26.17.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.26.26.png" alt="The file-based storage destination configuration view"></picture><figcaption></figcaption></figure>

In the advanced options you can choose the options mentioned below.

## URL format for Commandline

The file backend can be chosen with the `file://` prefix where the rest of the destination url is the path.

**Windows example:**

```
file://C:\Data
file://\\server\share\folder
```

**Linux/MacOS example:**

```
file:///home/user
```

For most cases it will also work without the `file://` prefix, but adding the prefix makes the intention clear.

## Improving speed for local filesystems

Since Duplicati is intended to be used with remote systems, it will make a temporary file, and then copy the temporary file to the new location. This enables various retry mechanisms, progress reporting and failure handling that may not be desired with local filesystems.

To change this logic to instead use the operating system `move` command to move the file into place, avoiding a copy, set the option `--use-move-for-put` on the file backend. Moving needs the whole file, so this option also turns off streaming transfers for the destination; `--disable-streaming-transfers` is not needed. With this option, all special handling will be removed and the transfer speed should be the optimal possible with the current operating system. Note that without streaming transfers no progress is shown during transfers, if you are using the UI, because the underlying move method cannot be monitored.

{% hint style="info" %}
In Duplicati 2.2.0.3 and earlier, `--use-move-for-put` only had an effect when `--disable-streaming-transfers` was also set.
{% endhint %}

### Concurrent transfers

A local folder is often an external hard disk, where several transfers at once are slower than one. For this reason, a local folder destination uploads one volume at a time, and a restore reads it with one volume downloader. To use more, set `--asynchronous-upload-limit` and `--restore-volume-downloaders`. Setting `--asynchronous-upload-limit=0` removes the limit.

{% hint style="info" %}
The one-at-a-time default for local folders was added in canary 2.4.1.102 and stable 2.4.1.1. Before that, `--asynchronous-upload-limit=0` allowed only one upload at a time, despite the help text saying it meant no limit.
{% endhint %}

### Linking instead of copying downloaded files

When a volume is read from the destination, for example to compact or restore, it is copied to the temporary folder. If the temporary folder is on the same volume as the destination, set `--use-hardlink-for-get` to create a hard link instead, which avoids copying large volumes. If the link cannot be made, the file is copied. As the link shares its contents with the stored file, this option also turns off streaming transfers for the destination.

### Disabling length verification

Because a local storage destination is expected to have a very low latency, the file backend will verify the length of the file after copy. This additional call is usually very fast and does not impact transfers speeds, but can be disabled for slightly faster uploads with `--disable-length-verification`.

### Verifying uploads

With `--list-verify-uploads`, Duplicati checks that each uploaded file is present on the destination. From canary 2.4.1.103, a local folder destination looks up the single file instead of listing the whole folder, which is much faster for a folder with many files. S3 destinations do the same with a `HEAD` request.

## Deleting files on full copy-on-write file systems

On copy-on-write file systems such as ZFS and Btrfs, deleting a file needs some free space, so a delete can fail when the file system is full. This can keep Duplicati from freeing up space by deleting old versions. From canary 2.4.1.103, set `--truncate-before-delete`, and a file that cannot be deleted is first overwritten with an empty file to free space, after which the delete is tried again. The option has no effect when `--soft-delete-prefix` is set.

## File name sanitation

Some mounted storage, such as SharePoint folders mounted on the local system, reports file names with extra null characters. To handle this, the file backend removes null characters at the start and end of the file names. If this causes problems with your storage, set `--disable-filename-sanitation` to use the file names exactly as the file system reports them.

## Removable drives (mostly Windows)

For removable drives, the mount path can sometimes change when inserting the drive. This is most prominent on Windows, where the drive letters are assigned based on what order the drives are connected. To support different paths, you can supply multiple alternate paths with `--alternate-target-paths`, where each path is separated with the system path separator (`;`on Windows, `:`on Linux/MacOS):

```
// Note, the paths are URL encoded here: E:\backupdata;G:\backupdata
file://F:\backupdata?alternate-target-paths=E%3A%5Cbackupdata%3BG%3A%5Cbackupdata
```

If you would like to support any drive letter, you can also use `*` as the drive letter (Windows only):

```
file://*:\backupdata
```

Because using multiple paths could end up attempting to make a backup to the wrong drive, you can use the option `--alternate-destination-marker` to provide a unique marker filename that needs to exist on the destination:

```
file://F:\backupdata?alternate-destination-marker=<filename>
```

Using this option will scan all paths provided, either using the `*` drive letter or `--alternate-target-paths`, and check if the folder contains a file with the given filename. Only the existence of the file is checked, not its contents.

The marker can also be used without alternate paths. This protects a network share or disk that is mounted on a folder: if the share is not mounted, the empty mount point does not have the marker file, and Duplicati does not write the backup into it.

## Authentication (Windows Only)

On Windows, the shares can be authenticated with a username and password (not with integrated authentication). This uses a [Windows API](https://learn.microsoft.com/en-us/windows/win32/api/winnetwk/nf-winnetwk-wnetaddconnection2a) to authenticate prior to accessing the share.

To use authentication, provide the `--auth-username` and `--auth-password`arguments to the query. Since the authentication in Windows is tied to the current user context, it is possible that the share is already mounted with different credentials, that may not have the correct permissions.

To guard against this, it is possible to drop the current authentication and re-authenticate prior to acessing the share. This can be done by adding the `--force-smb-authentication` option.
