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

## The `help` command

The commandline interface has full documentation for all supported options and some small examples for each of the supported operations. Running the help command will output the possible topics:

```
See duplicati-cli help <topic> for more information.
  General: example, changelog
  Commands: backup, sync, find, restore, delete, compact, test, compare, purge, vacuum
  Repair: repair, affected, list-broken-files, purge-broken-files
  Debug: debug, logging, create-report, test-filters, system-info, send-mail
  Targets: aliyunoss, azure, b2, box, cloudfiles, dropbox, ftp, aftp, file, gcs, googledrive, e2,
  jottacloud, mega, msgroup, onedrivev2, openstack, rclone, s3, ssh, od4b, mssp, sharepoint, sia,
  storj, tahoe, cos, webdav
  Modules: aes, gpg, zip, console-password-input, http-options, hyperv-options, mssql-options,
  runscript, sendhttp, sendxmpp, sendtelegram, sendmail
  Formats: date, time, size, decimal, encryption, compression
  Advanced: mail, advanced, returncodes, filter, filter-groups, <option>
  Secrets: secret, <provider>
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

| Option                | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--no-encryption`     | Use this to make an unencrypted mirror. Encryption is not supported for sync and must be disabled.                                                                                                                                                                                                                                                                                                                  |
| `--sync-remote-state` | Controls how the sync determines the remote state of each folder. `UseRemoteState` (default) enumerates the remote destination per folder (safest, one listing per folder). `UseLocalState` uses the local database and assumes it is up to date (faster, no extra listings). `BlindlyUpload` uploads every local file unconditionally without checking remote state (fastest, deletes are skipped with a warning). |
| `--sync-then-delete`  | Deletes files on the remote that no longer exist locally, keeping the destination an exact mirror of the source. Default is `false`. Has no effect with `--sync-remote-state=BlindlyUpload`.                                                                                                                                                                                                                        |
| `--sync-verify-hash`  | Verifies file content using SHA-256 hashes when size and time match, to detect files that changed without updating the timestamp. Default is `false`.                                                                                                                                                                                                                                                               |
| `--sync-recheck`      | Forces a full remote listing to rebuild the local sync cache. Under `UseLocalState` this effectively behaves as `UseRemoteState` for the run and refreshes the local inventory. Default is `false`.                                                                                                                                                                                                                 |

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

To restore the files from several versions at once, add `--restore-all-files=True` together with `--restore-path`. Each version is then restored into its own subfolder of the restore path, named after the time of the backup (such as `20261010-214426`). With `--restore-all-files=Unique`, a file is only restored the first time a given content is seen, so later versions only contain the files that changed. The versions can be limited with `--version`, and the files with filters. This option is available from Duplicati 2.4.0.0 and is not available in the user interface.

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
