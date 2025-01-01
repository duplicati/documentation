---
description: This page describes the SharePoint storage destination
---

# SharePoint Destination

Duplicati supports using Microsoft SharePoint as a storage destination. This page describes the SharePoint that uses the legacy API, for the [SharePoint provider that uses the Graph API, see SharePoint v2](sharepoint-v2-graph-api.md).

To use SharePoint, use the following URL format:

```
mssp://<folder>/<subfolder>
  ?auth-username=<username>
  &auth-password=<password>
```

## Integrated Authentication (Windows only)

If you are on Windows, it may be possible to use the current user's credentials to authenticate. Support for this is depending on many details and is not avaliable in all cases. To use integrated authentication, use the following URL format:

```
mssp://<folder>/<subfolder>?integrated-authentication=true
```

## Advanced options

Instead of deleting files directly, they can be moved to the recycle bin by setting the option `--delete-to-recycler.` This gives some additional safety if a version removal was unintended, but is not generally recommended, as it is a manual process to recover from a partial delete.&#x20;

The options `--web-timeout` and `--chunk-size` can be used to fine-tune performance that matches your setup, but generally it is recommended to keep them at their default values.

If you are running Duplicati in a data center with a very stable connection, you can use the option `--binary-direct-mode` to enable direct transfers for optimal performance.
