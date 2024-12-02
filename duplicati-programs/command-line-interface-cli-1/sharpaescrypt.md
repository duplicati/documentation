---
description: This page describes the SharpAESCrypt commandline encryption tool
---

# SharpAESCrypt

The SharpAESCrypt commandline tool uses the provided AES encryption library but exposes it as a commandline tool.

The SharpAESCryp tool is called `Duplicati.CommandLine.SharpAESCrypt.exe` on Windows and `duplicati-aescrypt` on Linux and MacOS. The library and commandline tool implements the [AESCrypt file format](https://www.aescrypt.com/aes_stream_format.html), so the commandline tool is compatible with any other tool using the [AESCrypt file format](https://www.aescrypt.com/download/).

To encrypt a file, use the syntax:

```
duplicati-aescrypt e <password> <plain-text file> <encrypted file>
```

And similarly to decrypt a file:

```
duplicati-aescrypt d <password> <encrypted file> <plain-text file>
```

For decryption, it is possible to use the "optimistic mode", which will leave the decrypted file on disk, even if it does not pass the validation. This is insecure, because the file contents can be modified if the integrity checks fail, but in some cases it can help to recover lost data:

```
duplicati-aescrypt do <password> <encrypted file> <plain-text file>
```

If you are encrypting files with a different tool, note that SharpAESCrypt adds an additional check, similar to [PCKS#7 padding](https://en.wikipedia.org/wiki/Padding_\(cryptography\)#PKCS#5_and_PKCS#7), which is not part of the AESCrypt specification. This does not change the file format, but makes it harder to inject trailing bytes. However, since other tools do not follow this standard, the decryption will reject such (otherwise valid) files. To decrypt such files, enable the compatibility mode:

```
duplicati-aescrypt dc <password> <encrypted file> <plain-text file>
```

To enable the compatibility check for regular Duplicati operations, add the environment variable:

```
AES_IGNORE_PADDING_BYTES=1
```
