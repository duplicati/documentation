---
description: This page describes the command line interface (CLI)
---

# Command Line Interface CLI

The commandline interface gives access to run all Duplicati operations without needing a server instance running. This is beneficial if your setup does not benefit from a UI and you want to use an external scheduler to perform the operations.

The binary is called `Duplicati.CommandLine.exe` on Windows and `duplicati-cli` on MacOS/Linux. All commands from the commandline interface follow the same structure:

```
duplicati-cli <command> <remote url> [arguments and options]
```

Each command also requires the option `--dbpath=<path to local database>`, but if this is not supplied, Duplicati will use a shared JSON file in the settings folder to keep track of which database belongs to each backup. Since there is no state given, the remote url is used as a key, because it is expected to uniquely identify each backup. If no entry is found, a new entry will be created and subsequent operations will use that database.

Most options have no relationship and can be applied in any order, but some options, mostly the filter options, are order sensitive and must be supplied in the order they are evaluated. The remote url is a url-like representation of the storage destination and options. The [destination overview](../backup-destinations/destination-overview.md) page provides an overview of what is currently supported.

The list of options that are supported is quite extensive and only the most common options are described on this page. For the sensitive options: `--passphrase`, `--auth-username`, and `--auth-password`, these can also be supplied through the matching environment variables: `PASSPHRASE`, `AUTH_USERNAME`, and `AUTH_PASSWORD`. For further safeguarding of these values, see the section on [using the secret provider](../detailed-descriptions/security-and-secrets/using-the-secret-provider/).

All commands support the `--dry-run` parameter that will simulate the operations and provide output, but not actually change any local or remote files.

From canary 2.4.1.103, pressing Ctrl+C stops the running operation cleanly, as the stop button in the UI does, so the local database is left in a good state. A second Ctrl+C aborts the operation, and a third ends the process at once. Earlier versions ended the process right away, which could leave the local database in need of a repair.

The scheduler options, such as `--disable-on-battery` and `--skip-missed-scheduled-backups`, are no longer listed by the command line from canary 2.4.1.103, as they only have an effect in the server. See [Scheduling and running backups in order](../detailed-descriptions/configuration-and-management/scheduling-and-running-backups-in-order.md).

{% hint style="info" %}
From canary 2.4.1.102, an option typed on the command line that Duplicati does not know, such as a misspelled option, stops the command with an error. Earlier versions gave a warning and ran the operation without the option. Options read from a parameters file, and jobs run from the UI, still only give a warning.
{% endhint %}

## The `help` command

The commandline interface has full documentation for all supported options and some small examples for each of the supported operations. Running the help command will output the possible topics:

```
See duplicati-cli help <topic> for more information.
  General: example, changelog
  Commands: backup, sync, find, restore, delete, compact, test, compare,
  purge, vacuum
  Searching: list-filesets, list-folder-contents, list-file-versions,
  search-files
  Repair: repair, affected, list-broken-files, purge-broken-files
  Locking: set-locks, read-lock-info
  Debug: debug, logging, create-report, test-filters, system-info, send-mail
  Targets: aliyunoss, azure, b2, box, duplicati, dropbox, ftp, aftp, file,
  gcs, googledrive, e2, jottacloud, mega, msgroup, movistarcloud, onedrivev2,
  openstack, rclone, s3, ssh, od4b, mssp, sharepoint, storj, tahoe, cos,
  webdav, pcloud, smb, cifs, filen, filejump, drimecloud
  Modules: aes, gpg, zip, common-options, console-password-input,
  httpreportstatus, hyperv-options, mssql-options, runscript, sendhttp,
  sendxmpp, sendmail, sendtelegram
  Formats: date, time, size, decimal, encryption, compression
  Advanced: mail, advanced, returncodes, filter, filter-groups, <option>
  Secrets: password, secret, <provider>
```

To list all options supported by the commandline interface, run the following command:

```
duplicati-cli help advanced
```

Note that the number of options is quite large, so you will likely need to use some kind of search functionality to navigate the output.

## Backup

The most common command is clearly the backup command, and the related restore command. To run a backup, use the following command:

```
duplicati-cli backup <remote url> <source path> [options]
```

The `source path` argument can be repeated to include multiple top-level folders. By default, backups are encrypted on the remote destination, and if no passphrase is supplied with `--passphrase`, the commandline interface will prompt for one. If the backups should be done unencrypted, provide the option `--no-encryption`.

The most common additional option(s) supplied are the filter options. The filters can selectively change what files and folders are excluded from the source paths. The [page on filters](../detailed-descriptions/security-and-secrets/filters-in-duplicati.md) describe the format of filters. Filters are supplied with the `--include` and `--exclude` options. For example:

```
--exclude=*.iso
--exclude=Thumbs.db
--exclude=*/tmp-*
```

When supplying only exclude filters, any file not matching will be included; likewise, if only includes are present, anything else will be included. The order of the arguments define the order the filters are evaluated. Beware that some symbols, such as `*` and `\` needs to be escaped on the commandline, and rules vary based on operating system and terminal application/shell.

If the destination limits how much can be transferred per day or month, use `--max-upload-per-run` to limit how much a single backup uploads, for example `--max-upload-per-run=5GB`. A value without a unit is in megabytes. When the limit is reached, the backup stops after the current file, as if stopped by the user, and is saved as a partial backup. The next run continues with the files that were not reached. The volumes that are being made or uploaded are finished, so a backup can upload somewhat more than the limit.

In the same way, `--max-backup-runtime` stops a backup after it has run for the given time, such as `--max-backup-runtime=4h`, and saves it as a partial backup that the next run continues. This keeps a long first backup from blocking the backups queued after it. A value without a unit is in seconds. When a full backup follows a partial backup, it is uploaded even if nothing changed, so the latest version is not left partial.

From canary 2.4.1.103, these backup options are also available:

| Option                         | Description                                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--keep-missing-sources`       | Keeps the files of a local source folder that does not exist, such as a folder on a drive that is not connected, as they were in the previous version, instead of recording them as deleted. This applies to local source paths only, and not to a source that lies inside another source.                                                                                         |
| `--skip-files-smaller-than`    | Leaves out files smaller than the given size, such as `1kb`. A file of exactly this size is backed up. Folders and metadata are not affected.                                                                                                                                                                                                                                      |
| `--disable-filetime-check-for` | Reads the files that match one or more filter expressions on every backup, as `--disable-filetime-check` does for all files. Use it for files that change without updating their time stamp or size, such as encrypted containers, for example `--disable-filetime-check-for=*.vc`. Separate several expressions with the path separator (`:` on Linux and macOS, `;` on Windows). |
| `--remote-filename-suffix`     | Appends a suffix to the name of each remote file, for storage that only accepts some file types. With `.bin`, a volume is stored as `duplicati-b<hash>.dblock.zip.aes.bin`. Files without the suffix are not seen, so use the same value to restore, repair or recreate the database, and set it only on a new backup or after renaming the existing files.                        |
| `--truncate-before-delete`     | If a remote file cannot be deleted, as can happen when a copy-on-write file system such as ZFS or Btrfs is full, the file is first overwritten with an empty file to free space, and the delete is tried again. Has no effect with `--soft-delete-prefix`.                                                                                                                         |
| `--gpg-encryption-recipient`   | Encrypts GPG volumes to a public key instead of with the passphrase. See [Encryption algorithms](../technical-details/understanding-backup/encryption-algorithms.md#gpg-encryption).                                                                                                                                                                                               |
| `--gpg-encryption-cipher-algo` | Chooses the cipher GPG encrypts with. See [Encryption algorithms](../technical-details/understanding-backup/encryption-algorithms.md#gpg-encryption).                                                                                                                                                                                                                              |

With `--list-verify-uploads`, backups to a local folder or to S3 now look up the single uploaded file instead of listing the whole folder.

The paths of deleted files and folders are now logged at the `Verbose` log level, so a report or log file can list them.

If either of the `--keep-time`, `--keep-versions`, or `--retention-policy` options are set, a successful backup will subsequently invoke the delete and compact operation as needed. This enables a single command to run all required maintenance, but can optionally be invoked as manual steps.

## Sync

{% hint style="info" %}
The sync command is available from version 2.3.1.0.
{% endhint %}

The sync command mirrors one or more source paths to a destination by copying the local files to the remote destination unencrypted. Unlike a backup, a sync keeps an exact mirror of the source on the destination: files are uploaded in their original (uncompressed, unencrypted) form, and with `--sync-then-delete` files that no longer exist locally are removed from the destination. To run a sync, use the following command:

```
duplicati-cli sync <remote url> <source path> [options]
```

The `source path` argument can be repeated to include multiple top-level folders. The copy is a one-way sync: source files are copied to the destination, but no changes on the destination are ever propagated back to the source.

{% hint style="warning" %}
Because the data is stored unencrypted, encryption must be disabled with `--no-encryption`. The sync operation maintains its own optional local database to track what has been uploaded.
{% endhint %}

Sources can be either local paths or remote sources. To use a remote source, prefix the source path with `@mount-path|` followed by the remote url, e.g. `@/tmp|ssh://hostname/path`. The sync command will enumerate the sources and copy all files to the destination. Snapshots are also supported; if snapshots are enabled, the copy is performed from the snapshot to ensure reliable reads. The sync command also supports multiple destinations.

The sync behavior is controlled by the following options:

| Option                              | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--no-encryption`                   | Use this to make an unencrypted mirror. Encryption is not supported for sync and must be disabled.                                                                                                                                                                                                                                                                                                                  |
| `--sync-remote-state`               | Controls how the sync determines the remote state of each folder. `UseRemoteState` (default) enumerates the remote destination per folder (safest, one listing per folder). `UseLocalState` uses the local database and assumes it is up to date (faster, no extra listings). `BlindlyUpload` uploads every local file unconditionally without checking remote state (fastest, deletes are skipped with a warning). |
| `--sync-then-delete`                | Deletes files and folders on the remote that no longer exist locally, keeping the destination an exact mirror of the source. A folder is deleted with everything in it, and from canary 2.4.1.103 folders are deleted as well as files. Files and folders that a filter excludes are not deleted. Default is `false`. Has no effect with `--sync-remote-state=BlindlyUpload`.                                       |
| `--sync-verify-hash`                | Verifies file content using SHA-256 hashes when size and time match, to detect files that changed without updating the timestamp. Default is `false`.                                                                                                                                                                                                                                                               |
| `--sync-snapshot-folders`           | Writes each run into its own folder below the destination, named after the UTC start time of the run (`yyyy-MM-ddTHHmmssZ`). Each snapshot folder holds a complete copy of the source. Unchanged files are hard linked from the previous snapshot when the destination supports it (`file://` and `smb://`), otherwise copied. `--sync-then-delete` has no effect in this mode.                                     |
| `--sync-keep-snapshots`             | With `--sync-snapshot-folders`, the number of snapshot folders to keep. After a successful run, the oldest snapshot folders beyond this number are deleted. Zero keeps all snapshot folders.                                                                                                                                                                                                                        |
| `--sync-snapshot-disable-hardlinks` | With `--sync-snapshot-folders`, copies unchanged files into each snapshot instead of hard linking them, so changing a file in one snapshot never changes it in another. Takes more space and time.                                                                                                                                                                                                                  |
| `--sync-keep-modification-time`     | Gives the uploaded files the modification time of the local files instead of the time of the upload. Works with a local folder, and with WebDAV servers that accept the `X-OC-Mtime` header, such as Nextcloud and ownCloud. Other destinations keep the time of the upload.                                                                                                                                        |
| `--sync-recheck`                    | Forces a full remote listing to rebuild the local sync cache. Under `UseLocalState` this effectively behaves as `UseRemoteState` for the run and refreshes the local inventory. Default is `false`.                                                                                                                                                                                                                 |

As with the backup command, the `--include` and `--exclude` filters can be used to selectively change what files and folders are included from the sources. All commands also support the `--dry-run` parameter to simulate the operation without changing any local or remote files.

## Restore

The restore command is equally as important as the backup command and can be executed with:

```
duplicati-cli restore <remote url> <filename> <options>
```

The restore command in this form will restore the specified file(s) to their original location. If a file is already present in the original location, the files will be restored with a timestamp added to their name. If no files are specified, or the filename is `*`, all files will be restored.

To restore to a different location than the original, such as to a staging folder, use the option `--restore-path=<destination>`. The restore will find the shortest common path for the files to restore, and make a minimal folder structure to restore into.

If you are sure you want to restore the files, and potentially lose existing files, use the option `--overwrite`.

The restore command will restore from the latest version of the backup, but other versions can be selected with the `--version=<version>`. As with backups, the `--include` and `--exclude` options can be used to filter down the desired files to restore.

From canary 2.4.1.100 and stable 2.4.1.1, a restore that could not restore some of the files exits with code 3, in the same way as a backup that finished with errors. Earlier versions exited with code 0. A block that cannot be read from its volume only fails the files that use it, and the other files are restored.

To restore the files from several versions at once, add `--restore-all-files=True` together with `--restore-path`. Each version is then restored into its own subfolder of the restore path, named after the time of the backup (such as `20261010-214426`). With `--restore-all-files=Unique`, a file is only restored the first time a given content is seen, so later versions only contain the files that changed. The versions can be limited with `--version`, and the files with filters. This option is available from Duplicati 2.4.0.0 and is not available in the user interface.

From canary 2.4.1.103, `--restore-files-modified-after` restores only the files that were modified at or after a point in time, according to the modification time stored in the backup. The value can be a date, such as `2026-01-01`, or an interval back from now, such as `1M`. Older files are not restored, and are not read or checked in the restore target. Folders and symbolic links are restored as usual.

A restore keeps a list of every file and block to restore in temporary tables, which are in memory by default. For a restore of millions of files these can use several gigabytes of memory. Add `--restore-temp-tables-on-disk` to keep them in a file in the temporary folder instead, which uses less memory but can be slower. From canary 2.4.1.103, a restore of many files also uses much less memory without this option, as the files are read in batches.

## Find

The find command is responsible for locating files within the backups:

```
duplicati-cli find <remote url> <filename> <options>
```

If no filename is specified, the command will instead list all the known backup versions (or "snapshots). Multiple filenames can be specified, and they are all treated as [filter expressions](../detailed-descriptions/security-and-secrets/filters-in-duplicati.md). If a full file path is specified, the find command will instead list all versions of that file.

To list files in a specific version, use the `--version=<version>` option. To search across all versions, use the `--all-versions` option.

As with backup and restore, the `--include` and `--exclude` filters can be added to assist in narrowing down the search output.

A related operation is the "compare" command, which will show a summary of differences between two versions.

## Handling exceptional situations

For normal uses, it should be sufficient to only use the backup, restore, and find commands. However, in some exceptional cases, it may be needed to manually fix the problem. If such a situation occurs, Duplicati will abort the backup and give an error message that indicates the problem.

### Repair

If the local database is missing or somehow out-of-sync with the remote storage, it can be rebuilt with the repair command. The repair command is invoked with:

```
duplicati-cli repair <remote url>
```

If the local database is missing, it is recreated from the remote storage. If the local database is present, the repair command will attempt to recreate any data that is missing on the remote storage. Recreate is only possible if the missing data is still available on the local system. If the required data is missing, the repair command will fail with an error message, explaining what is missing.

From canary 2.4.1.103 and stable 2.4.1.1, a repair that recreates a missing dblock volume reads source files that are locked by other programs through a snapshot (VSS on Windows, LVM on Linux), when `--snapshot-policy` is set. A repair with `--dry-run` works without a local database, and the number of versions and the size of the destination shown in the user interface are updated after a repair. From canary 2.4.1.103, a repair with `--dry-run` also no longer changes the local database.

### List broken files

The command list-broken-files will check which remote files are missing or damaged and report what files can no longer be restored due to this:

```
duplicati-cli list-broken-files <remote url> <options>
```

The related command "affected" can give a similar output where reports what files would be lost, if the given remote files were damaged. It is possible that files can be partially restored despite damaged remote files. For handling partial restore, see the section on [disaster recovery](../technical-details/understanding-restore/disaster-recovery.md).

### Purge broken files

If the remote files cannot be recovered, but you would like the backup to continue, you can use the purge-broken-files command to rewrite the remote storage to simply exclude the files that are no longer restorable:

```
duplicati-cli purge-broken-files <remote url> <options>
```

After successfully purging the broken files, the local database and remote storage will be in sync and you can continue backups.

The related command "purge" can be used to selectively remove files from the backup.

After purging files, you can run the compact command to release space that was held by the removed files.
