---
description: This page describes the Dropbox storage destination
---

# Dropbox Destination

Duplicati supports using [Dropbox](https://www.dropbox.com) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on Dropbox and does not store files so they are individually accessible from Dropbox.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.15.00.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.14.53.png" alt="View of the configuration of the Dropbox destination"></picture><figcaption></figcaption></figure>

To configure the Dropbox destination you need to pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format from Commandline

To use Dropbox, use the following URL format:

```
dropbox://<folder>/<subfolder>?authid=<authid>
```

To use Dropbox you must first obtain an `AuthID` by using a Duplicati service to log in to Dropbox and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.
