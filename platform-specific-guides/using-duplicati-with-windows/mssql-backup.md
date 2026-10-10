---
description: This page describes how to make backups of MSSQL databases
---

# MSSQL backup

On Windows, Duplicati has built-in support for finding MSSQL Servers if running with sufficient privileges. To include MSSQL databases in a backup, visit the [Source Data](../../getting-started/set-up-a-backup-in-the-ui.md#id-2.-source-data) step, and find "Microsoft SQL Servers" in the treeview:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.28.26.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.27.05.png" alt=""></picture><figcaption></figcaption></figure>

By choosing the top-level "Microsoft SQL Servers", all MSSQL databases will be included in the backup. If new databases are added later, these will automatically be included.

## Exclude a Database

If you want to exclude one or more databases, expand the "Microsoft SQL Servers" entry, then expand the Server entry, and click any database to toggle its inclusion status:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.27.39.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.27.21.png" alt=""></picture><figcaption></figcaption></figure>

This way of setting it up will mean that new databases will automatically be backed up, but the items you deselected will not be backed up. Note the filters to the right are automatically populated when clicking the databases. The filter relies on the name of both the server and the database, so renaming either will require the setup to be changed.

## Include only specific databases

If you prefer to manually specify databases to include so new databases are not automatically included, make sure the "Microsoft SQL Servers" element is not selected, then expand the element, expand the server name, and click the specific databases that you want to include in the backup:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.27.56.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.28.08.png" alt=""></picture><figcaption></figcaption></figure>

This will ensure that new databases will not automatically be included. Notice the "Paths" section on the right is automatically filled with the server- and database names. If you rename either the server or the database, you will need to edit the backup.

## Backup of active MSSQL databases

To support backup of active MSSQL databases, Duplicati relies on [Volume Shadow Copy Service (VSS)](https://learn.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service). The VSS setup is a series of steps that ensures that the data Duplicati backs up is consistent and can be restored without issues.&#x20;

{% hint style="warning" %}
Using VSS requires the `SE_BACKUP` privilege which often means running Duplicati as Administrator, or as a [Windows Service](run-duplicati-as-a-windows-service.md)
{% endhint %}

In short, the sequence is as follows:

* Duplicati asks for a snapshot
* VSS asks all VSS-aware application to flush to disk
* Applications flush and refrain from updating the disk
* VSS creates a snapshot, and tells all applications to resume
* Anything written to disk while the snapshot is active uses Copy-on-write
* Duplicati reads the consistent snapshot and releases it after the backup ends

This requires a small amount of extra disk and memory to store the data that changes while the snapshot is active, but this is released once the snapshot is released. The end result is that MSSQL databases can continue to run with no downtime, and Duplicati can read a consistent copy.

To ensure that MSSQL databases are correctly backed up, change the advanced option `--snapshot-policy` to `Required`:&#x20;

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.29.30.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.29.18.png" alt=""></picture><figcaption></figcaption></figure>

This setting makes Duplicati abort if it is not able to secure a VSS snapshot.

