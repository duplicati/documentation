---
description: This page describes the FTP storage destination
---

# FTP Destination

The FTP protocol is widely supported but generally, [FTP is considered a legacy protocol with security issues](https://serverfault.com/questions/1163510/ftp-for-what-it-is-used-for-why-its-urls-sometimes-begin-with-http-and-i) despite correct implementation. Due to its continued ubiquity, it is still supported by Duplicati using [FluentFTP](https://github.com/robinrodricks/FluentFTP).

To use the FTP backend, you can use a URL such as:

```
ftp://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
```

Despite FTP being a well documented standard, there are many different implementations of the protocol, so the FTP backend supports a variety of settings for configuring the connection. You can use a non-standard port through the hostname, such as `ftp://hostname:2121` .

## Connection mode

Due to the way FTP is working, it requires multiple connections to transfer data, and the method for selecting which mode has a number of quirks. The default setting is "AutoPassive" which works great for most setups, leaving the burden of configuring the firewall to the server.

Use the option `--ftp-data-connection-type` to choose a specific connection mode if the default does not work for your setup.

## Encryption  mode

To enable encrypted connections, you can use the option `--ftp-encryption-mode` and setting it to either `Implicit` or `Explicit`. The `Implicit` setting creates a TLS connection and everything is encrypted, where `Explicit` is more commonly used, and creates an unencrypted connection and then upgrades to an encrypted session.

The default setting is `--ftp-encryption-mode=None` which uses unencrypted FTP connections.

The setting `--ftp-encryption-mode=Auto` is the most compatible setting, but also insecure, as it connects in unencrypted mode and then attempts to switch to encrypted, but will continue in unencrypted mode if this fails.

To further lock down the encryption mode, the option `--ftp-ssl-protocols` can be used to limit the accepted protocols. **Note:** that due to unfortunate naming in .NET, the option `--ftp-ssl-protocols=None` means "use the system defaults".&#x20;

### Self-signed certificates

To support self signed certificates, the FTP destination also supports the `--accept-specified-ssl-hash` option is also supported which takes an SHA1 certificate digest and approves the certificate if it matches that hash. This is similar to a manual certificate pinning and allows trusting a specific certificate outside the operating systems normal trust chain.&#x20;

For testing, it is also possible to use `--accept-any-ssl-certificate` which will bypass certificate checks completely and enable man-in-the-middle attacks on the connection.

## Path resolution

The FTP protcol is tied to a Posix-style path where `/` is the root folder and subfolders are described using the forward-slash separator. On some systems the filesystem is virtual, so the user can only see the root path, but has no knowledge of the underlying real filesystem. On others, the paths are mapped directly to the user home, like `/home/user`.&#x20;

Use the option `--ftp-absolute-path` to treat the source path as an absolute path, meaning that `folder` maps to `/folder` and not to `/home/user/folder`.&#x20;

A related option is the `--ftp-use-cwd-names` option that makes Duplicati keep track of the working directory and uses the FTP server's `CD` command to set the working folder prior to making a request.

## Verification of uploads

To verify that uploads actually work, the FTP connection will request the file after it has been uploaded to check that it exists and has the correct file size. This check is usually quite fast and does not impact backup speeds, but if needed it can be disabled with `--disable-upload-verify`.&#x20;

A related setting `--ftp-upload-delay` adjusts the delay that is inserted after the upload but before verifying the file exists, which is required on some servers to ensure the file is fully flushed before validating the existence.

## Debugging commands

Because the FTP protocol can sometimes be difficult to diagnose, the option `--ftp-log-to-console` will enable logging various diagnostics output to the terminal. This option works best with the [BackendTool](../../duplicati-programs/command-line-interface-cli-1/backendtool.md) or [BackendTester](../../duplicati-programs/command-line-interface-cli-1/backendtester.md) application. The option `--ftp-log-privateinfo-to-console` will also enable logging of usernames and passwords being transmitted, to further track down issues. Neither option should be set outside of testing and evaluation scenarios.

## Notes on `aFTP`

Prior to Duplicati 2.1.0.2 there were two different FTP backends, FTP and Alternative FTP (aFTP). This was done as the primary FTP backend was based on [FtpWebRequest](https://learn.microsoft.com/en-us/dotnet/api/system.net.ftpwebrequest?view=net-9.0) and was lacking some features. The aFTP backend was introduced to maintain the FTP backend but offer more features using the FluentFTP library.

With Duplicati 2.1.0.2 the codebase was upgraded to .NET8 which means that `FtpWebRequest` is now deprecated. For that reason, the FTP backend was converted to also be based on FluentFTP, so both FTP backends are currently using the same library.

The `aFTP` backend is still available for backwards compatibility, but is the same as the FTP backend, with some different defaults. The `aFTP` backend will likely be marked deprecated in a future version, and eventually removed.





