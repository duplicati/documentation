---
description: This page describes the OneDrive storage destination
---

# OneDrive Destination

Duplicati supports using Microsoft OneDrive as a storage destination. Note that Duplicati stores compressed and encrypted volumes on OneDrive and does not store files so they are individually accessible from OneDrive.

To use OneDrive, use the following URL format:

```
onedrivev2://<folder>/<subfolder>?authid=<authid>
```

To use OneDrive you must first obtain an `AuthID` by using a Duplicati service to log in to Microsoft and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Drive ID

A default drive will be used to store the data. If you require another drive to be used to store data, such as a shared drive, use the `--drive-id=<drive id>` option.
