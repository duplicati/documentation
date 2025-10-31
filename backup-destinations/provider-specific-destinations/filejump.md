---
description: This page describes the Filejump integration
---

# Filejump

Duplicati supports using [Filejump](https://filejump.com) as the storage destination.&#x20;

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
