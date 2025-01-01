---
description: This page describes the OneDrive For Business storage destination
---

# OneDrive For Business Destination

Duplicati supports using Microsoft OneDrive for Business as a storage destination. Note that Duplicati stores compressed and encrypted volumes on OneDrive and does not store files so they are individually accessible from OneDrive.

To use OneDrive For Business, use the following URL format:

```
od4b://<folder>/<subfolder>
  ?auth-username=<username>
  &auth-password=<password>
```

## Integrated Authentication (Windows only)

If you are on Windows, it may be possible to use the current user's credentials to authenticate. Support for this is depending on many details and is not avaliable in all cases. To use integrated authentication, use the following URL format:

```
od4b://<folder>/<subfolder>?integrated-authentication=true
```

## Advanced options

Instead of deleting files directly, they can be moved to the recycle bin by setting the option `--delete-to-recycler.` This gives some additional safety if a version removal was unintended, but is not generally recommended, as it is a manual process to recover from a partial delete.&#x20;

The options `--web-timeout` and `--chunk-size` can be used to fine-tune performance that matches your setup, but generally it is recommended to keep them at their default values.

If you are running Duplicati in a data center with a very stable connection, you can use the option `--binary-direct-mode` to enable direct transfers for optimal performance.
