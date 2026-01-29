---
description: >-
  Duplicati supports locking remote files to prevent deletion and this page
  describes how and when you should use remove locking.
---

# Using remote file locking

Duplicati supports Object Locking, also known as WORM (Write Once, Read Many), for compatible storage providers. When enabled, this feature ensures that your backup files cannot be deleted, modified, or overwritten for a specific period, even if your backup credentials are compromised.

{% hint style="info" %}
Remote file locking was introduced in Canary 2.2.0.103
{% endhint %}

***

### Why Use Remote File Locking?

Remote file locking provides a "last line of defense" for your data. In a standard backup scenario, if an attacker gains access to your backup credentials, they can delete your offsite backups before encrypting your local data. Object locking prevents this.

#### Use Cases

* Ransomware Protection: Even if a ransomware strain finds your storage credentials, it cannot delete the locked backup volumes. Your data remains safe on the server until the lock expires.
* Legal Hold & Compliance: For industries with strict data retention requirements (like HIPAA or GDPR),Compliance Mode ensures that data is preserved exactly as it was recorded for a mandatory duration.
* Protection Against Accidental Deletion: Prevents accidental cleanup or bucket emptying by administrators.

***

### How it Works

When you enable locking, Duplicati instructs the storage provider to apply a retention period to every file it uploads (`dblock`, `dindex`, and `dlist`). Since the storage provider is responsible for the actual locking, Duplicati and the machine it runs on does not have any way to unlock the files.

#### Retention Modes

| Mode                            | Description                                                                                                                                                                                                   | Flexibility             |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| <p>Governance<br>(Unlocked)</p> | <p>Files are locked, and cannot be deleted even if the storage credentials are leaked.<br><br>Accounts with specific high-level permissions (Administrsators) can still bypass the lock if necessary.<br></p> | High (Good for testing) |
| <p>Compliance<br>(Locked)</p>   | No one can delete the files, including the root account holder. The lock cannot be shortened.                                                                                                                 | None (Strict security)  |

{% hint style="danger" %}
WARNING: Use Compliance Mode with extreme caution. If you set a 10-year lock in Compliance Mode, those files cannot be deleted by you, the storage provider, or anyone else until the time expires. You will be billed for that storage for the entire duration, and there is no "undo" button.
{% endhint %}

***

### Configuration Guide

#### 1. Prerequisites

Ensure your storage provider and bucket support Object Locking. This must often be enabled at the time of bucket creation. For instance, AWS S3 and B2 requires that Versioning is enabled for the bucket before locking can be enabled.

Supported Backends:

* Amazon S3 (and S3-compatible like IDrive e2)
* Backblaze B2
* Azure Blob Storage
* Google Cloud Storage

#### 2. Enabling Locking in a Backup Job

To enable locking for a backup, add the following option to your configuration (Step 5 "Options" in the WebUI):

`--remote-file-lock-duration=30D`

This example locks every new file for 30 days. You can use units like `H` (hours), `D` (days), `W` (weeks), or `M` (months).

{% hint style="info" %}
Always test this with compliance mode (default) and use a short duration until you are sure it works as intended.
{% endhint %}

#### 3. Provider-Specific Settings

You can specify the mode depending on your provider:

* S3: `--s3-object-lock-mode=governance` (default) or `compliance`
* Backblaze B2: `--b2-retention-mode=governance` or `compliance`
* Azure: `--azure-blob-immutability-policy-mode=unlocked` or `locked`
* Google Cloud Storage: `--gcs-retention-policy-mode=unlocked` or `locked`&#x20;

{% hint style="info" %}
**Google Cloud Storage** also requires using `--service-account-file` or `--service-account-json` as the default OAuth flow does not grant permissions to lock objects
{% endhint %}

***

### Managing Locks via CLI

If you have existing backups that you want to lock retrospectively, or if you need to audit your locks, use the Duplicati Command Line. You can also use the Commandline part of the UI to execute these commands.

#### Apply locks to existing versions

To lock files belonging to the most recent backup:

```bash
duplicati-cli set-locks <storage-url> --remote-file-lock-duration=1M --version=0
```

#### Sync lock status to local database

If you changed locks manually on the server, update Duplicati’s local records:

```shellscript
duplicati-cli read-lock-info <storage-url> --refresh-lock-info-complete=true
```

***

### Important Storage Considerations

#### Soft Deletion (S3, B2 and others)

In S3 and B2, the buckets have versioning enabled for locking to work. When Duplicati "deletes" a file (e.g., during a compaction process), the provider creates a Delete Marker. The file seems to disappear from your file list, but the locked data is still there taking up space. This happens regardless of the file being locked or not.

To avoid paying for data that should have been deleted, you must configure Lifecycle Rules on your bucket to permanently remove non-current versions once the lock expires.

#### Direct Deletion (Azure and others)

In Azure, the file remains visible and cannot be deleted. If Duplicati attempts to delete a locked file, the operation will simply fail because the blob itself is protected. In this case, no lifecycle rules are needed, because the file will actually be deleted when requested.

#### Mixed (Google Cloud Storage)

Google Cloud Storage can use both a WORM approach as well as a soft-delete and/or versioning approach. Depending on the bucket settings you will get either a soft-delete approach or a WORM setup. If you disable versioning and soft-delete, but enable object lock retention, this gives a WORM behavior that rejects deletes. Enabling soft-delete will allow the files to be marked as deleted but they are not actually deleted before the lock expires. Additionally, the soft-delete rules may keep the objects in the bucket even after the lock expires.
