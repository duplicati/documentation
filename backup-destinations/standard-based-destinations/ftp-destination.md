---
description: This page describes the FTP storage destination
---

# FTP Destination

The FTP protocol is widely supported but generally, [FTP is considered a legacy protocol with security issues](https://serverfault.com/questions/1163510/ftp-for-what-it-is-used-for-why-its-urls-sometimes-begin-with-http-and-i) despite correct implementation. Due to its continued ubiquity, it is still supported by Duplicati.

To use the FTP backend, you can use a URL such as:

```
ftp://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
```

Despite FTP being a well documented standard, there are many different implementations of the protocol, so the FTP backend supports a variety of settings for configuring the connection.

## Connection mode



## Encryption  mode



## Path resolution



