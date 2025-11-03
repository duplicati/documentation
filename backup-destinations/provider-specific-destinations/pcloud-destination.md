---
description: This page describes the pCloud storage destination
---

# pCloud Destination

Duplicati supports using [pCloud](https://pcloud.com) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on pCloud and does not store files so they are individually accessible from pCloud.

The pCloud provider was added in Duplicati v2.1.0.100, and is included in stable release 2.2.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.35.49.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.35.43.png" alt="Configuring pCloud destination"></picture><figcaption></figcaption></figure>

To configure the pCloud destination you need to first choose if you are working with the Global or EU servers. Then pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format for Commandline

To use pCloud, use the following URL format:

```
pcloud://<host>/<folder>/<subfolder>?authid=<authid>
```

The `<host>` value must be one of:

* `api.pcloud.com` for US based access
* `eapi.pcloud.com` for EU based access

To use pCloud you must first obtain an `AuthID` by using a Duplicati service to log in to pCloud and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

Due to the way the pCloud authentication system is implemented, the generated AuthID is not stored by the OAuth server and cannot be revoked via the OAuth server. To revoke the token, you must revoke the Duplicati app from your pCloud account, which will revoke all issued tokens.

This also means that after issuing the pCloud token, you do not need to contact the OAuth server again, unlike other OAuth solutions.
