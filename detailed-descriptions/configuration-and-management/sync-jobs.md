---
description: This page describes sync jobs, which copy files to a destination without making a backup
---

# Sync jobs

{% hint style="info" %}
Sync jobs are available from Duplicati 2.4.0.0.
{% endhint %}

A regular backup job stores the files in compressed and encrypted volumes, keeps multiple versions, and deduplicates the data. A sync job instead copies the files to the destination as they are, so the destination holds a plain copy of the source that can be used without Duplicati.

Sync jobs are one-way: the source is copied to the destination, but changes on the destination are never copied back. Sync jobs support the same sources as backups, including [remote sources](remote-sources.md) and snapshots, and can have [multiple destinations](multiple-backup-destinations.md).

{% hint style="warning" %}
The files on the destination of a sync job are **not encrypted**. Only use destinations where you are comfortable storing the files in plain form.
{% endhint %}

## Creating a sync job in the UI

Start setting up a new backup as described in [Set up a backup in the UI](../../getting-started/set-up-a-backup-in-the-ui.md). On the first step, set "Operation mode" to "Sync" instead of "Backup". The encryption settings are then hidden, and the "Options" step does not show the retention settings, as a sync job keeps no versions.

The operation mode can only be chosen when the job is created. Backup and sync jobs store data in different ways, so an existing job cannot be changed from one to the other.

## How files are synced

By default, Duplicati lists the destination before copying to find out which files need to be uploaded. Files and folders that were deleted from the source are kept on the destination unless `--sync-then-delete` is set. With `--sync-then-delete`, a folder that is gone from the source is deleted on the destination together with everything in it. Box, Dropbox, Google Drive, OneDrive, S3 and iDrive e2 delete such a folder in one call; other destinations delete the files in it one by one. Files and folders that a filter excludes are not deleted.

{% hint style="warning" %}
Before 2.4.1.103, `--sync-then-delete` deleted only files and left the folders on the destination. A job that already uses the option will now also remove folders that no longer exist in the source.
{% endhint %}

The sync can also keep the modification times of the local files with `--sync-keep-modification-time`, for local folders and for WebDAV servers that accept the `X-OC-Mtime` header, such as Nextcloud and ownCloud.

## Snapshot folders

By default, a sync job keeps a single copy of the source on the destination. To keep earlier states as well, set `--sync-snapshot-folders`. Each run then writes into its own folder below the destination, named after the UTC start time of the run, such as `2026-11-14T083000Z`. Every snapshot folder holds a complete copy of the source and can be browsed directly. A marker file in the destination root names the latest completed snapshot.

On `file://` and `smb://` destinations, unchanged files are hard linked from the previous snapshot, so they do not take up extra space. On other destinations they are uploaded again. Set `--sync-snapshot-disable-hardlinks` to always copy them, so that changing a file in one snapshot never changes it in another.

Use `--sync-keep-snapshots=<number>` to delete the oldest snapshot folders after a successful run, keeping the given number. Zero, the default, keeps all snapshot folders. `--sync-then-delete` has no effect with snapshot folders, as each snapshot starts empty. If a destination holds snapshot folders and a job runs without `--sync-snapshot-folders` but with `--sync-then-delete`, the run is refused instead of deleting the snapshots.

## Restoring from a sync job

The destination of a sync job holds plain files, so they can be copied back without Duplicati. The new UI can also restore from a sync job in the same way as from a backup, and can configure the sync mode for secondary destinations of a job with [multiple destinations](multiple-backup-destinations.md).

## Sync options

The options that control the sync, such as `--sync-remote-state` and `--sync-verify-hash`, are described in the [sync section of the commandline page](../../duplicati-programs/command-line-interface-cli.md#sync). They can be added as advanced options to a sync job in the UI.
