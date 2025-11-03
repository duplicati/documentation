---
description: This page describes the Filen.io integration
---

# Filen.io

Duplicati supports using [Filen.io](https://filen.io) as the storage destination since stable release v2.2. Note that Duplicati encrypts volumes before uploading them to Filen.io, but will encrypt them again using the Filen encryption scheme so they can be downloaded from Filen.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.08.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.16.png" alt="Configuring the Filen.io destination"></picture><figcaption></figcaption></figure>

To configure the destination for Filen.io, choose a unique path for the backup, and then provide the username and password to authenticate.

## URL format for Commandline

To use Filen.io, use the following URL format:

```
filen://<folder>/<subfolder>?auth-username=email&auth-password=*****
```

You can also supply a Two-Factor code if your account is protected by 2FA, but note that you need to type in a new 2FA code each time you access the storage as Filen does not have support for API keys.

Duplicati only supports the V2 Auth protocol and will only encrypt data using the version `002`  encryption mode. There is experimental support for reading data encrypted with version `003` if you need to upload files outside of Duplicati.
