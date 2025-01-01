---
description: This page describes the SharePoint v2 storage destination
---

# SharePoint v2 (Graph API)

Duplicati supports using Microsoft SharePoint as a storage destination. This page describes the SharePoint that uses the Graph API, for the [SharePoint provider that uses the legacy API, see SharePoint](sharepoint-destination.md).

To use SharePoint, use the following URL format:

```
sharepoint://<folder>/<subfolder>
  ?authid=<authid>
  &site-id=<site-id>
```

To use SharePoint v2 you must first obtain an `AuthID` by using a Duplicati service to log in to Microsoft and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Performance tuning options

If you need to gain more performance you can fine-tune the performance of chunked transfers with the options:

* `--fragment-size`
* `--fragment-retry-count`
* `--fragment-retry-delay`

For most uses, it is recommended that these are kept at their default settings and only changed after confirming that there is a gain to be made by changing them.
