---
description: This page describes the Box.com storage destination
---

# Box.com Destination

Duplicati supports using [box.com](https://box.com) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on box.com and does not store files so they are individually accessible from box.com.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.30.58.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.31.06.png" alt="View for configuring box.com destination"></picture><figcaption></figcaption></figure>

To configure the box.com destination you need to pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format for Commandline

To use box.com, use the following URL format:

```
box://<folder>/<subfolder>?authid=<authid>
```

To use box.com you must first obtain an `AuthID` by using a Duplicati service to log in to box.com and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Fully delete files

When files are deleted from your box.com account, they will be placed in the trash folder. To avoid old files taking up storage in your account, you can add `--box-delete-from-trash` which will then also remove the file from the trash folder.
