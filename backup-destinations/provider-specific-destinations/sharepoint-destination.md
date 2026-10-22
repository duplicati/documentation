---
description: This page describes the deprecated SharePoint storage destination that uses the legacy API
---

# SharePoint (legacy API)

{% hint style="danger" %}
This backend is **deprecated** as Microsoft has shut down the API it was calling. Use the [SharePoint](sharepoint-v2-graph-api.md) backend (formerly "SharePoint v2") which uses the Microsoft Graph API. Migrate existing backups to the new backend.
{% endhint %}

Duplicati supports using [Microsoft SharePoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration) as a storage destination. This page describes the deprecated destination that uses the legacy SharePoint API (`mssp://`). For the current destination that uses the Microsoft Graph API, see [SharePoint](sharepoint-v2-graph-api.md).

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.39.46.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.39.58.png" alt="Configure Sharepoint destination"></picture><figcaption></figcaption></figure>

To configure the SharePoint destination, enter the values for: server, path on server, account name, and access key.

## URL format for Commandline

To use SharePoint, use the following URL format:

```
mssp://<folder>/<subfolder>
  ?auth-username=<username>
  &auth-password=<password>
```

## Integrated Authentication (Windows only)

If you are on Windows, it may be possible to use the current user's credentials to authenticate. Support for this is depending on many details and is not available in all cases. To use integrated authentication, use the following URL format:

```
mssp://<folder>/<subfolder>?integrated-authentication=true
```

## Advanced options

Instead of deleting files directly, they can be moved to the recycle bin by setting the option `--delete-to-recycler.` This gives some additional safety if a version removal was unintended, but is not generally recommended, as it is a manual process to recover from a partial delete.&#x20;

The options `--web-timeout` and `--chunk-size` can be used to fine-tune performance that matches your setup, but generally it is recommended to keep them at their default values.

If you are running Duplicati in a data center with a very stable connection, you can use the option `--binary-direct-mode` to enable direct transfers for optimal performance.
