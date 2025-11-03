---
description: This page describes the SharePoint v2 storage destination
---

# SharePoint v2 (Graph API)

Duplicati supports using [Microsoft SharePoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration) as a storage destination. This page describes the SharePoint that uses the Graph API, for the [SharePoint provider that uses the legacy API, see SharePoint](sharepoint-destination.md).

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.40.40.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.40.26.png" alt="Configure the SharePoint v2 destination"></picture><figcaption></figcaption></figure>

To configure the SharePoint v2 destination you need to pick a unique folder name for the backups, provide the site id, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format for Commandline

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
