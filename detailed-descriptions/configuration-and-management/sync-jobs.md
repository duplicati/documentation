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

By default, Duplicati lists the destination before copying to find out which files need to be uploaded. Files that were deleted from the source are kept on the destination unless `--sync-then-delete` is set. Folders are never deleted from the destination.

The options that control the sync, such as `--sync-remote-state` and `--sync-verify-hash`, are described in the [sync section of the commandline page](../../duplicati-programs/command-line-interface-cli.md#sync). They can be added as advanced options to a sync job in the UI.
