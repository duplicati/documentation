---
description: >-
  This page describes how to configure Duplicati Storage, including the storage
  region, object locking for the organization and for each machine, and how to
  migrate existing storage to a bucket with object locking.
---

# Duplicati Storage configuration

[Duplicati Storage](duplicati-storage.md) works without any configuration, but the Duplicati Console has a few settings that control where the data is stored and how it is protected. The settings are found in the Duplicati Console under **Settings → Storage**, and cover:

- The **default region**, which decides where the data for new machines is stored.
- The **default object lock**, which makes uploaded backup files immutable for a period of time.
- **Per-machine object lock settings**, which override the organization defaults for a single machine.
- **Bucket migration**, which moves the storage for a machine to a new bucket with object locking.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.10.24.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.10.12.png" alt="The storage settings at the top of the Storage page"></picture><figcaption></figcaption></figure>

## How the storage is organized

Each machine that uses Duplicati Storage has its own storage container, called a **bucket**. The bucket is created the first time the machine stores data, and it is created in the region and with the object lock settings that are in effect for the machine at that time. All backups made by the machine are stored in the same bucket, separated by the backup ID.

This is important to understand when changing the settings, as some properties of a bucket can only be chosen when the bucket is created:

| Setting                   | New machines         | Machines that already store data                                                     |
| ------------------------- | -------------------- | ------------------------------------------------------------------------------------ |
| Region                    | Uses the new setting | Stays in the region the bucket was created in                                        |
| Object lock on or off     | Uses the new setting | Unchanged, the bucket must be [migrated](#migrate-a-bucket-to-object-lock) to add it |
| Retention mode and period | Uses the new setting | Updated right away, if the bucket already has object lock                            |

## Default region

The **Default region** selects where the data is stored for machines that start using Duplicati Storage. The list has the following types of entries:

- **Auto (Nearest)** picks the data center closest to the machine when the machine makes the first backup. This is the default.
- **A region**, such as **EU (nearest)**, **USA (nearest)** or **Asia (nearest)**, picks the nearest data center within that area. Use this when the data must stay within an area, for instance for compliance reasons, but the exact location is not important.
- **A specific data center**, grouped by area and shown with the country flag. Use this when the data must be stored in a specific location.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.13.38.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.11.30.png" alt="The region picker with areas and data centers" width="375"></picture><figcaption></figcaption></figure>

A data center marked with an info icon is currently not available for new backups. Machines that already store data in it are not affected.

The region of each machine is shown next to the machine name in the machine list. Changing the default region does not move existing data.

## Object lock

Object lock, also known as WORM (Write Once, Read Many), makes the uploaded backup files immutable for a retention period. While a file is locked, it cannot be deleted or overwritten by anyone using the storage API key, so the backups are protected even if the machine, or the credentials it uses, are compromised.

Object lock also keeps the previous versions of files that are deleted or overwritten, which makes it possible to [restore from a point in time](../detailed-descriptions/security-and-secrets/restoring-from-a-point-in-time.md) if the backup files are damaged.

{% hint style="info" %}
Object lock is applied by the storage itself, not by Duplicati. Neither the machine, the Duplicati Console nor Duplicati support can unlock a file before the retention period has expired.
{% endhint %}

### Organization settings

The current setting is shown on the Storage page under **Default object lock**, either as **Enabled** with a summary such as `Governance · 30 days default retention`, or as **Disabled**. Click **Change** to edit the settings.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.13.19.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.13.04.png" alt="The Default object lock dialog" width="563"></picture><figcaption></figcaption></figure>

The dialog has the following settings:

| Setting           | Description                                                                                                                                                                                        |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Object lock       | **Enabled** or **Disabled**. When enabled, new buckets are created with object lock.                                                                                                               |
| Retention mode    | **Governance** (default) or **Compliance**. See [Retention modes](#retention-modes) below.                                                                                                         |
| Default retention | **No default retention**, or **Fixed number of days**. With a fixed number of days, every uploaded file is locked for that many days. The number of days must be a whole number greater than zero. |

The retention mode and default retention are only shown when object lock is enabled.

When the settings are saved, they are used for all new buckets, and the retention mode and default retention of existing buckets that already have object lock are updated to match. Machines with their own [machine settings](#machine-settings) keep the values they override.

### Retention modes

| Mode       | Description                                                                                                                                                                                |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Governance | Locked files cannot be deleted or overwritten with the storage API key. Privileged users at the storage provider can lift the lock. This is the default, and is recommended to start with. |
| Compliance | Locked files cannot be deleted or overwritten by anyone, including Duplicati, until the retention period has expired. The lock period cannot be shortened.                                 |

{% hint style="warning" %}
In compliance mode, locked files cannot be deleted by anyone until their retention period has expired, and the storage they use is counted towards the quota until then. Start with governance mode and a short retention period, and only switch to compliance mode once you are sure the settings are right.
{% endhint %}

The Duplicati Console does not currently offer a way to lift a governance lock, so for both modes, locked files stay in the storage until the lock expires. Additional controls for removing files with governance lock will be added in a future update.

### Default retention

With a **default retention**, the storage locks every new file for the configured number of days as soon as it is uploaded. No changes are needed on the machine, as the lock is applied by the storage.

With **No default retention**, object lock is available, but files are only locked when the Duplicati client asks for it. To do this, add the `--remote-file-lock-duration` option to the backup, as described in [Using remote file locking](../detailed-descriptions/security-and-secrets/using-remote-file-locking.md). This makes it possible to use a different lock period for each backup.

{% hint style="info" %}
Choose a retention period that is shorter than the period Duplicati keeps the backup versions for. When Duplicati deletes an old backup version or compacts the backup, the files it removes stay in the storage until their lock expires, and the storage they use counts towards the quota until then.
{% endhint %}

### Storage usage with object lock

When object lock is enabled, a file that is deleted or overwritten is not removed right away. The storage keeps the previous version, which is what makes point-in-time restore possible, and removes it with a daily cleanup once it is no longer locked:

- A file that is **not locked** is removed within about 24 hours after it was deleted.
- A file that is **locked** is removed within about 24 hours after the lock expires.

Until then, the storage used by the file is still counted in the storage usage and towards the quota. The dialog shows a warning about this when object lock is enabled.

### Disabling object lock

Object lock cannot be removed from a bucket once it is enabled. When object lock is disabled for the organization or a machine:

- New buckets are created without object lock.
- The default retention is removed from existing buckets, so new uploads are no longer locked automatically.
- Files that are already locked stay locked until their retention expires.
- Earlier versions of deleted files are still kept and cleaned up as described above.

## Machine settings

Each machine can override the organization settings. In the machine list on the Storage page, open the actions menu for the machine and choose **Object lock settings**.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.18.14.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.18.26.png" alt="The object lock dialog for a machine" width="563"></picture><figcaption></figcaption></figure>

The dialog has the same settings as for the organization, but each setting also has the option **Inherit from organization**, which shows the current organization value in parentheses. An inherited setting follows the organization setting if it is changed later. Choose a specific value to override it for the machine.

The machine settings can be used to, for instance:

- Enable object lock only for the machines that need it, while keeping it disabled for the organization.
- Use compliance mode, or a longer retention, for a machine with critical data.
- Use **No default retention** for a machine where the lock period is set per backup with `--remote-file-lock-duration`.

When the machine settings are saved, the bucket for the machine is updated right away, if it already has object lock. If object lock is enabled for a machine whose bucket was created without it, the bucket must be [migrated](#migrate-a-bucket-to-object-lock).

### Object lock status for a machine

The machine list shows the object lock status next to each machine name. Hover over the status to see the details, including whether the settings come from the organization or from a machine override.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.21.04.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.20.53.png" alt="The object lock status and its tooltip in the machine list"></picture><figcaption></figcaption></figure>

| Status                  | Description                                                                                                                                                        |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Governance / Compliance | Object lock is enabled for the storage of the machine, with the retention mode shown.                                                                              |
| Not locked              | Object lock is not enabled for the machine.                                                                                                                        |
| Object lock not applied | Object lock is enabled in the settings, but the bucket for the machine was created without it. [Migrate the bucket](#migrate-a-bucket-to-object-lock) to apply it. |
| Migrating bucket        | A bucket migration is running for the machine. Backups and restores on the machine fail until it completes.                                                        |

## Migrate a bucket to object lock

Object lock can only be enabled when a bucket is created. For a machine that already stores data in a bucket without object lock, the storage must be moved to a new bucket. The Duplicati Console does this with a **bucket migration**.

The option is available when the machine shows **Object lock not applied**. To start a migration:

1. Make sure object lock is enabled for the machine, either through the organization settings or the machine settings, and that the retention mode and default retention are as intended.
2. Open the actions menu for the machine and choose **Migrate bucket to object lock**.
3. Read the confirmation dialog, and click **Migrate bucket**.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.23.18.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.22.50.png" alt="The confirmation dialog for a bucket migration" width="563"></picture><figcaption></figcaption></figure>

The migration runs in the background with the following steps:

1. The storage API keys for the machine are revoked, so nothing is written to the storage while the migration runs.
2. A new bucket is created with the object lock settings for the machine.
3. Every file is copied to the new bucket, and the copy is verified.
4. The default retention is applied to the new bucket, and the machine is switched to it.
5. The old bucket is deleted.

When the migration has completed, the machine receives new credentials for the new bucket the next time it connects to the storage, and the backups continue as before. No changes are needed in the backup configuration.

{% hint style="warning" %}
Before migrating, be aware of the following:

- Backups and restores on the machine **fail while the migration runs**. Copying can take a long time for large buckets, so plan the migration for a time where the machine does not need to run backups.
- The storage is **counted twice** until the old bucket is deleted, which may bring the organization over the quota for a while.
- The files that are copied are **not locked**. Only new files uploaded **after** the migration are locked with the default retention. Files that were copied can be locked afterwards from the Duplicati client with `--remote-file-lock-duration`.
- The migration **cannot be reverted**. Object lock cannot be removed from the new bucket, and files locked in it stay locked for their retention period.
  {% endhint %}

### Follow the migration

While the migration runs, the machine shows the status **Migrating bucket**. To see the progress, open the actions menu for the machine and choose **Bucket migration status**.

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2026-10-22 at 11.25.50.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2026-10-22 at 11.26.34.png" alt="The bucket migration status dialog" width="563"></picture><figcaption></figcaption></figure>

The dialog shows the number of files and bytes copied, the names of the old and the new bucket, and when the migration started and finished. The migration goes through the following states:

| State                       | Description                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------- |
| Starting                    | The migration is recorded and the access for the machine is being revoked.                              |
| Copying objects             | The new bucket is created, and the files are being copied.                                              |
| Switching to the new bucket | All files are copied and verified, and the machine is being switched to the new bucket.                 |
| Deleting the old bucket     | The machine uses the new bucket, and the old bucket is being deleted.                                   |
| Completed                   | The migration is done, and the machine uses the new bucket.                                             |
| Failed                      | The migration was stopped, and the machine still uses the old bucket. The error is shown in the dialog. |

If the migration fails before the machine is switched, the machine continues to use the old bucket with all the data, and the new bucket is removed. Check the error in the status dialog, and start the migration again when the cause has been fixed.

If the migration completes, but the old bucket could not be deleted, the dialog shows that the old bucket still counts towards the storage usage. The deletion is retried automatically.

Only one migration can run for a machine at a time.

## Deleting storage with object lock

[Deleting stored backups](duplicati-storage.md#delete-stored-backups) from the Storage page works with object lock, but locked files cannot be removed. If any of the files are still locked, the delete fails with a message that the backup data could not be deleted, and the backup registration is kept. Retry the delete when the lock has expired.

## Troubleshooting

| Message                                                                            | Cause and solution                                                                                                                                                                |
| ---------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The default lock retention must be a positive number of days                       | The default retention is set to a fixed number of days, but the number is zero or negative. Enter a whole number of days greater than zero.                                       |
| Object Lock is not enabled for the machine; enable it before migrating its bucket  | A migration was started for a machine that does not have object lock enabled. Enable object lock for the organization or the machine first.                                       |
| The machine has no bucket yet; its bucket is created with Object Lock on first use | The machine has not stored any data yet, so no migration is needed. The bucket is created with the object lock settings when the machine makes the first backup.                  |
| The machine's bucket already has Object Lock                                       | The bucket already has object lock, so no migration is needed.                                                                                                                    |
| A bucket migration is already running for the machine                              | Wait for the running migration to complete, and check the progress with **Bucket migration status**.                                                                              |
| The storage for machine ... is being migrated; try again later                     | A backup or restore was started while the bucket for the machine is being migrated. Wait for the migration to complete, and the backups will continue.                            |
| Failed to delete the backup data from storage; the backup registration was kept    | Some of the files are still locked. Retry the delete when the lock has expired.                                                                                                   |
| Object lock not applied                                                            | Object lock is enabled for the machine, but the bucket was created without it. [Migrate the bucket](#migrate-a-bucket-to-object-lock), or set the machine to not use object lock. |
