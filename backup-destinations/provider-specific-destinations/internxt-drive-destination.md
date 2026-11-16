---
description: This page describes the Internxt Drive storage destination
---

# Internxt Drive Destination

{% hint style="info" %}
The Internxt Drive destination is available from canary 2.4.1.102.
{% endhint %}

Duplicati can store backups in [Internxt Drive](https://internxt.com/drive). Files are encrypted on the client with the account key, in the same way as the official Internxt clients do. This is in addition to the encryption Duplicati applies to the volumes.

Internxt does not offer an official API for this. The destination follows the public source code of the Internxt CLI, and therefore needs an Internxt plan that includes CLI access. If the plan does not include it, the connection fails with a message that says so.

Internxt also offers S3-compatible storage. If you have that, you can use it with the [S3-compatible destination](../standard-based-destinations/s3-compatible-destination.md) instead.

## URL format for Commandline

To use Internxt Drive, use the following URL format:

```
internxt://<folder>/<subfolder>?auth-username=<email>&auth-password=<password>
```

## Two-factor authentication

If the account is protected by two-factor authentication, Duplicati needs a code each time it logs in. The login lasts about three days, so for scheduled backups a single code is not enough.

* `--two-factor-secret` is the secret key the authenticator app was enrolled with, which is the text behind the QR code. With it, Duplicati computes a fresh code for every login, so scheduled backups keep working. Note that the second factor is then stored together with the password.
* `--two-factor-code` is a single code from the authenticator app. A new code is needed for every login, so this only suits a one-time run.

## Other options

* `--move-to-trash`: moves deleted files to the Internxt trash instead of deleting them permanently.
* `--multipart-threshold`: files larger than this size are uploaded in multiple parts. The default is `100MiB`, which is also the lowest value Internxt accepts.
* `--multipart-part-size`: the size of each part in a multipart upload. Each part is held in memory while it is uploaded. The default is `30MiB`, and values below `5MiB` are raised to `5MiB`.
* `--api-url`: the base URL of the Internxt gateway. The default is `https://gateway.internxt.com`.
* `--client-name`: the value sent in the `internxt-client` header. Internxt restricts API access by client and plan, so this can be changed if the default is rejected.

If the account limits the size of each file, a volume larger than the limit is refused with a message giving the limit. Lower the [remote volume size](../../technical-details/understanding-backup/backup-size-parameters.md#remote-volume-size) of the backup to stay below it.
