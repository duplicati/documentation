---
description: This page describes the Mega.nz storage destination
---

# Mega.nz Destination

{% hint style="warning" %}
The destination is currently using the [MegaApiClient](https://github.com/gpailler/MegaApiClient) which is **no longer maintained**. Since there is little documentation on how to integrate with Mega.nz, it is not recommended that this storage destination is used anymore.
{% endhint %}

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.33.11.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.32.59.png" alt="Configure the Mega.nz destination"></picture><figcaption></figcaption></figure>

To configure the Mega.nz backend, enter a unique path for the backup to be stored at, the username and password.

## URL format for Commandline

To use the [Mega.nz](https://mega.io) storage destination, you can use the following URL format:

```
mega://<folder>/<subfolder>
  ?auth-username=<username>
  &auth-password=<password>
```

## Two-factor authorization

If the account has two-factor authentication enabled, provide the shared secret with the option `--auth-two-factor-key`. This is the Base32 string shown when two-factor authentication was set up on the account (the value behind the QR code), not the 6-digit code from the authenticator app. Duplicati uses the secret to compute the current TOTP code on each login, so the value does not change and is suitable for automated backups.
