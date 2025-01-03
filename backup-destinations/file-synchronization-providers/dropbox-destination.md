---
description: This page describes the Dropbox storage destination
---

# Dropbox Destination

Duplicati supports using [Dropbox](https://www.dropbox.com) as a storage destination. Note that Duplicati stores compressed and encrypted volumes on Dropbox and does not store files so they are individually accessible from Dropbox.

To use Dropbox, use the following URL format:

```
dropbox://<folder>/<subfolder>?authid=<authid>
```

To use Dropbox you must first obtain an `AuthID` by using a Duplicati service to log in to Dropbox and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.
