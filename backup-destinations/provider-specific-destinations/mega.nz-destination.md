---
description: This page describes the Mega.nz storage destination
---

# Mega.nz Destination

To use the [Mega.nz](https://mega.io) storage destination, you can use the following URL format:

```
mega://<folder>/<subfolder>
  ?auth-username=<username>
  &auth-password=<password>
```

**NOTE:** The destination is currently using the [MegaApiClient](https://github.com/gpailler/MegaApiClient) which is **no longer maintained**. Since there is little documentation on how to integrate with Mega.nz, it is not recommended that this storage destination is used anymore.

## Two-factor authorization

It is possible to provide a two-factor key with the option `--auth-two-factor-key` but since this value changes often, it is not suitable to use in most automated backup settings. This is a design choice from Mega.nz and cannot be fixed by Duplicati.
