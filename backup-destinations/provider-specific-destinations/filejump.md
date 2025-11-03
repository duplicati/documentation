---
description: This page describes the Filejump integration
---

# FileJump

Duplicati supports using [FileJump](https://filejump.com) as the storage destination since stable release v2.2.

{% hint style="warning" %}
As of 2025-11-01 FileJump has announced that they will change the solution including the API so Duplicati will likely stop working with Filejump on 2025-12-31. If API docs are updated before, Duplicati may be updated to support FileJump again. Until this happens, we do not recommend using Filejump with Duplicati.
{% endhint %}

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.47.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.39.png" alt="Configure the destination for FileJump"></picture><figcaption></figcaption></figure>

To configure the FileJump destination, enter a unique path for the backup and an [API token](https://drive.filejump.com/account-settings).

## URL format for Commandline

To use Filejump, use the following URL format:

```
filejump://<hostname>/<folder>/<subfolder>?api-token=*****
```

You can get an API key by visiting your [Filejump account settings](https://drive.filejump.com/account-settings).

It is also possible to use a username/password combination, but this is not recommeded as it it does not work with 2FA enabled on the account:

```
filejump://<hostname>/<folder>/<subfolder>?auth-username=email&auth-password=*****
```

If you use the username/password method, Duplicati will create and use an API token, but will negotiate with Filejump to get the token, which may slow things down a bit.
