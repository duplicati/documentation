---
description: This page describes how Duplicati handles encryption
---

# Encryption Algorithms

The modular design in Duplicati makes it simple to add new encryption methods, if needed. By default, Duplicati ships with a built-in [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-based encryption and a [GPG](https://en.wikipedia.org/wiki/GNU_Privacy_Guard)-based encryption that requires GPG binaries to be present on the system.

## AES Encryption

The built-in [AES encryption is an open-source C# port](https://github.com/duplicati/sharpaescrypt) of the open-specification [AESCrypt file format](https://www.aescrypt.com). The file format ensures that each volume is encrypted with a separate random file-encryption key (FEK), which makes it harder to perform analysis on the encrypted volumes. The file format also adds a [HMAC](https://en.wikipedia.org/wiki/HMAC) signature that prevents modifications to the encrypted volume.

### AES Crypt format versions

Duplicati 2.4 can write two versions of the AES Crypt format. Version 2 is the format used by all earlier versions. Version 3 uses a stronger key derivation with a configurable number of iterations (`--aes-v3-iterations`, default `300000`).

Duplicati 2.4 still writes version 2 unless `--aes-version=3` is set (the `duplicati-cli help aes` output lists `3` as the default, but version 2 is what is written). Volumes in version 3 can only be read by Duplicati 2.4 and later (and the 2.3.1 beta), so do not switch to version 3 if the backup must remain readable by older versions, for instance for a downgrade. Duplicati reads both versions regardless of the setting, so existing volumes keep working after a change.

The option `--aes-minimal-header` leaves out the optional metadata (creator, timestamp) from the file header.

## GPG Encryption

With GPG encryption the implementation requires the GPG binaries to be installed on the system. GPG is available on most Linux distributions, and in most package managers, but can otherwise be obtained via the [GnuPG download page](https://www.gnupg.org/download/).

By default, GPG encryption is done with symmetric encryption and default options ([CAST-128](https://en.wikipedia.org/wiki/CAST-128)), using the provided passphrase. It is possible to supply custom arguments to GPG, to choose a different algorithm, or even set it up to perform asymmetric encryption where only the public key is present on the client.
