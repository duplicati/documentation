---
description: This page describes the Filen.io integration
---

# Filen.io

Duplicati supports using [Filen.io](https://filen.io) as the storage destination since stable release v2.2. Note that Duplicati encrypts volumes before uploading them to Filen.io, but will encrypt them again using the Filen encryption scheme so they can be downloaded from Filen.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.08.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.41.16.png" alt="Configuring the Filen.io destination"></picture><figcaption></figcaption></figure>

To configure the destination for Filen.io, choose a unique path for the backup, and then provide the username and password to authenticate.&#x20;

If you press the "Authenticate now" button, Duplicati will autenticate you with Filen and extract an API key that can be used for further communication. If your account is using 2FA, you must enter the code before pressing "Authenticate now".&#x20;

The API key is stored in the advanced option `--api-key`. Once you have obtained an API key from Filen, the 2FA code is no longer required.

## URL format for Commandline

To use Filen.io, use the following URL format:

```
filen://<folder>/<subfolder>?auth-username=email&auth-password=*****
```

If your account uses 2FA, you can supply the code with `--two-factor-code`, but a new code is needed each time Duplicati authenticates. Instead, supply an API key with `--api-key`, which works even if the account uses 2FA. You can obtain the API key with the Filen CLI tool. The username and password are still required when using an API key.

By default, deleted files are removed permanently. Set `--move-to-trash` to move them to the Filen trash instead.

Duplicati only supports the V2 Auth protocol and will only encrypt data using the version `002` encryption mode. There is experimental support for reading data encrypted with version `003` if you need to upload files outside of Duplicati.
