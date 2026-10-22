---
description: >-
  This page describes how to use bucket versioning, optionally combined with
  object locking, to restore a backup as it was at a point in time after the
  remote files were damaged or deleted.
---

# Restoring from a point in time

If the backup destination keeps old versions of each file, Duplicati can read the destination as it looked at an earlier point in time. This makes it possible to restore from a backup after the remote files have been deleted, overwritten or damaged, for instance by ransomware, leaked credentials, or an accidental cleanup, without first having to repair the files on the storage.

{% hint style="info" %}
Point-in-time restore was introduced in Canary 2.4.0.105
{% endhint %}

---

### How it works

When versioning is enabled on a bucket, the storage provider never really overwrites or deletes an object. Uploading a file with an existing name creates a new version, and deleting a file adds a _delete marker_ on top of the existing versions. The file disappears from a normal listing, but the earlier versions are still stored.

The `--remote-point-in-time` option makes Duplicati use these versions. When the option is set, Duplicati lists the destination and downloads files as they were at the given time, picking the version of each file that was current at that time and ignoring files that were deleted before it. Files that were created after the chosen time are not visible.

While the option is set, the destination is **read-only**. Any attempt to upload, delete, rename or lock a file is refused, so the old state cannot be changed by mistake while you are restoring from it.

Versioning on its own keeps the old versions, but anyone with sufficient access to the bucket can still delete them. To make sure the versions are still there when you need them, combine versioning with [object locking](using-remote-file-locking.md), so the versions cannot be removed until the lock expires.

---

### Supported destinations

| Destination                                                                                                 | Requirements                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| [Amazon S3](../../backup-destinations/provider-specific-destinations/amazon-s3-destination.md)              | Versioning enabled on the bucket. Object locking is recommended.                                                                             |
| [S3-compatible storage](../../backup-destinations/standard-based-destinations/s3-compatible-destination.md) | The provider must support versioning and listing object versions. The default AWS client must be used, `--s3-client=minio` is not supported. |
| [Duplicati Storage](../../duplicati-console/duplicati-storage.md)                                           | Object locking must be enabled for the storage.                                                                                              |

The credentials used for the restore must be allowed to list and read object versions. For Amazon S3 this means the `s3:ListBucketVersions` and `s3:GetObjectVersion` permissions, in addition to the permissions needed for a normal restore.

---

### Step 1: Protect the destination

Point-in-time restore can only bring back versions that still exist, so the destination must be prepared **before** an incident happens. Enable versioning on the bucket, and decide how the files should be locked. There are two ways to do this, and both work with point-in-time restore.

#### Option A: Lock from the storage side

With this approach, the storage itself locks every file when it is uploaded, and nothing needs to be configured in Duplicati.

On Amazon S3 and most S3-compatible providers, this is done by enabling Object Lock on the bucket and setting a **default retention** period, for example 30 days in Governance or Compliance mode. Every object that is uploaded is then locked for the default period.

The advantage of this approach is that the protection does not depend on the machine that makes the backup. Even if the machine and its credentials are compromised, the attacker cannot turn the locking off, as long as the backup credentials are not allowed to change the bucket configuration.

{% hint style="success" %}
**Duplicati Storage** supports object locking with a default lock period. When locking is enabled for the storage, every file that is uploaded is locked automatically, and point-in-time restore works without setting up buckets, versioning or lifecycle rules yourself. See [Duplicati Storage](../../duplicati-console/duplicati-storage.md) for more information.
{% endhint %}

#### Option B: Lock from the client side

With this approach, Duplicati sets the lock on each file it uploads. Enable versioning and object locking on the bucket, but without a default retention, and add the lock duration to the backup configuration:

```
--remote-file-lock-duration=30D
```

This gives more control from Duplicati, and the lock duration can be different for each backup. It also works with Duplicati Storage when locking is enabled for the storage. See [Using remote file locking](using-remote-file-locking.md) for the details, including the provider-specific lock modes.

#### Choosing the retention period

The lock duration, and any lifecycle rule that removes old versions, decides how far back in time you can restore. Choose a period that is longer than the time it may take to discover an incident. If ransomware went unnoticed for two weeks, a 7-day lock is not enough to restore to a time before the incident.

{% hint style="warning" %}
If you use lifecycle rules to remove non-current versions, as described in [Using remote file locking](using-remote-file-locking.md#soft-deletion-s3-b2-and-others), make sure the rules keep the versions for at least as long as you want to be able to restore from.
{% endhint %}

---

### Step 2: Restore after an incident

When something has gone wrong with the destination, use a direct restore from the backup files with the `--remote-point-in-time` option. A direct restore builds a temporary database from the remote files, so it does not depend on the local database, which may be out of sync with the damaged destination.

#### Before you start

- **Stop the backups** that use the destination. A backup, compact or repair running against the damaged destination can make the situation harder to understand, and may delete versions that are not locked.
- **Find a time before the incident.** Pick a time where the destination was known to be good, for example the time of the last successful backup before the incident. If a backup was running at the chosen time, pick an earlier time, so the restore does not see a half-finished backup.

#### Choosing the point in time

The option accepts either a duration, which counts back from now, or an absolute date and time:

| Value                                         | Meaning                                       |
| --------------------------------------------- | --------------------------------------------- |
| `--remote-point-in-time=3D`                   | The destination as it was 3 days ago          |
| `--remote-point-in-time=2D4h`                 | The destination as it was 2 days, 4 hours ago |
| `--remote-point-in-time=2026-10-14T08:00:00Z` | The destination as it was at the given time   |

A duration is converted to an absolute time when the operation starts, so the point in time stays fixed during a long restore. A time in the future reads the current state.

#### Restore from the user interface

1. Open the Duplicati user interface and choose **Restore**.
2. Choose **Direct restore from backup files** and click **Start**.
3. On the **Backup destination** page, enter the destination details as for the original backup. For Duplicati Storage, use a [restore connection string](../../duplicati-console/duplicati-storage.md#restore-with-a-restore-connection-string) if the original machine is not available.
4. Add the advanced option `remote-point-in-time` to the destination and set it to the chosen time, for example `3D`. Note that the UI currently only accepts days; use the "Edit as text" to type something else.
5. Continue with the encryption passphrase, select the backup version and the files to restore, and start the restore as described in [Restoring files](../../getting-started/restoring-files.md).

The backup versions shown are the ones that existed at the chosen time. If the list looks wrong, or files are reported missing, go back and try a different time.

#### Restore from the command line

Add the option to a normal restore command:

```sh
duplicati-cli restore "s3://my-bucket/backup?auth-username=<key id>&auth-password=<secret>" "*" \
  --remote-point-in-time=2026-10-14T08:00:00Z \
  --restore-path="/path/to/restore" \
  --passphrase="<passphrase>"
```

To check which backup versions and files are available at the chosen time before restoring, use the `list` and `find` commands with the same option:

```sh
duplicati-cli find "<destination url>" "*" \
  --remote-point-in-time=3D \
  --passphrase="<passphrase>"
```

For Duplicati Storage, use the restore connection string with the backup ID as the destination URL, as described in [Restore from the command line](../../duplicati-console/duplicati-storage.md#restore-from-the-command-line).

---

### After the restore

The point-in-time option only affects reading. It does not undo the changes on the storage, so the current state of the destination is still the damaged one. Once the data is restored:

- Find and fix the cause of the incident, and rotate any credentials that may have been leaked.
- Start a new backup to a clean destination, or a new folder in the bucket, rather than continuing the old backup on top of the damaged files.
- Keep the old bucket, with its versions, until you are sure that everything needed has been restored.

---

### Troubleshooting

| Message                                                                                        | Cause and solution                                                                                                                                                                  |
| ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The destination is read-only while --remote-point-in-time is set                               | An operation tried to change the destination. Only use the option for restores and other read-only operations, such as `list` and `find`, and remove it again afterwards.           |
| Reading the destination at a point in time with --remote-point-in-time requires the AWS client | The backup uses `--s3-client=minio`. Set `--s3-client=aws` for the restore.                                                                                                         |
| Reading the destination at a point in time is not available for this account                   | The Duplicati Storage account does not have object locking enabled, so no earlier versions are kept.                                                                                |
| The value ... for --remote-point-in-time is not a valid time                                   | The value is not a valid duration or date. Use a duration such as `3D` or a date such as `2026-10-14T08:00:00Z`.                                                                    |
| File ... not found at ...                                                                      | The file did not exist at the chosen time, either because it was created later or the old versions were removed. Try a different time, and check the lifecycle rules on the bucket. |
