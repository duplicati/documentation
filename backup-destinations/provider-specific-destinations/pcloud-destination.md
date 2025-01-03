---
description: This page describes the pCloud storage destination
---

# pCloud Destination

Duplicati supports using [pCloud](https://pcloud.com) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on pCloud and does not store files so they are individually accessible from pCloud.

The pCloud provider was added in Duplicati v2.1.0.100, and is not yet included in a stable release.

To use pCloud, use the following URL format:

```
pcloud://<folder>/<subfolder>?authid=<authid>
```

To use Dropbox you must first obtain an `AuthID` by using a Duplicati service to log in to pCloud and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

Due to the way the pCloud authentication system is implemented, the generated AuthID is not stored by the OAuth server and cannot be revoked via the OAuth server. To revoke the token, you must revoke the Duplicati app from your pCloud account, which will revoke all issued tokens.

This also means that after issuing the pCloud token, you do not need to contact the OAuth server again, unlike other OAuth solutions.
