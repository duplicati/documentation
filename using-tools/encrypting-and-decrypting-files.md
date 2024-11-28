---
description: >-
  This page describes how to work with encrypted files outside of normal
  operations
---

# Encrypting and decrypting files

In normal Duplicati operations, the files at the remote destination should never be handled by anything but Duplicati. Changing the remote files will always result in warnings or errors when Duplicati needs to access those files.

However, in certain exceptional scenarios, it may be required that the file contents are accessed manually.

## Processing files encrypted with AES encryption

The files encrypted with the default AES encryption follows the [AESCrypt](https://www.aescrypt.com/aes_stream_format.html) file format, so [any tool that supports the AESCrypt file format](https://www.aescrypt.com/download/) can be used to decrypt and encrypt these files.

For convenience, Duplicati also ships with a command line binary named [SharpAESCrypt](../duplicati-programs/command-line-interface-cli-1/sharpaescrypt.md) that uses the same library that is used by Duplicati. This tool can be used to decrypt the remote volume files with the encryption passphrase, as well as encrypt files.

## Processing files encrypted with GPG encryption

Files encrypted with [GPG](https://www.gnupg.org/index.html) can choose one of the many ways, and a general overview of how GPG works can be found in the [GPG man-pages](https://www.gnupg.org/documentation/manuals/gnupg24/gpg.1.html). When using the default options, Duplicati will use the symmetric mode for GPG. In this mode, you can use this command to decrypt a file:

```
gpg -d volume.zip.gpg -o volume.zip
```

And similarly, to encrypt a file, you can use:

```
gpg --symmetric volume.zip -o volume.zip.gpg
```

## Re-compress and re-encrypt

If you need to switch from GPG to AES, or vice-versa, you can use the [Recovery Tool](../duplicati-programs/command-line-interface-cli-1/recoverytool.md) to automatically process all files on the storage destination. The recovery tool also supports recompressing or changing the compression method.

If you use this method, make sure to [recreate the local database](recovering-from-failure.md).

