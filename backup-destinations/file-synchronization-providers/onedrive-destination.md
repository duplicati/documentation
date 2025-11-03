---
description: This page describes the OneDrive storage destination
---

# OneDrive Destination

Duplicati supports using [Microsoft OneDrive](https://www.microsoft.com/da-dk/microsoft-365/onedrive/online-cloud-storage) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on OneDrive and does not store files so they are individually accessible from OneDrive.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.16.01.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.15.53.png" alt="View of the configuration page for the OneDrive destination"></picture><figcaption></figcaption></figure>

To configure the OneDrive destination you need to pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format from Commandline

To use OneDrive, use the following URL format:

```
onedrivev2://<folder>/<subfolder>?authid=<authid>
```

To use OneDrive you must first obtain an `AuthID` by using a Duplicati service to log in to Microsoft and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Drive ID

A default drive will be used to store the data. If you require another drive to be used to store data, such as a shared drive, use the `--drive-id=<drive id>` option.
