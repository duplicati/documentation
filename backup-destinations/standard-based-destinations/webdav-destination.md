---
description: This page describes the WebDAV storage destination
---

# WebDAV Destination

The WebDAV protocol is a minor extension to the HTTP protocol used for web requests. Because it is compatible with HTTP it also supports SSL/TLS certificates and verification similar to what websites are using.

To use the WebDAV destination, you can use a url such as:

```
webdav://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
```

You can supply a port through the hostname, such as `webdav://hostname:8080/path`.

## Authentication method

There are three different authentication methods supported with WebDAV:

* Integrated Authentication (mostly on Windows)
  * Use `--integrated-authentication=true`to enable. This works for some hosts on Windows and most likely has no effect on other systems as it requires a Windows-only extension to the request and a server that supports it.
* Digest Authentication&#x20;
  * Use `--force-digest-authentication=true` to use Digest-based authentication
* Basic Authentication
  * Sending the username and password in plain-text. This is the default, but is insecure if not using an SSL/TLS encrypted connection.

You need to examine your destination servers documentation to find the supported and recommended authentication method.

## Encryption and Certificates

To use an encrypted connection, add the option `--use-ssl=true`such as:

```
webdav://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
  &use-ssl=true
```

This will then use an HTTPS secured connection subject to the operating system certificate validation rules. If you need to use a self-signed certificate that is not trusted by the operating system, you can use the option `--accept-specified-ssl-hash=<hash>` to specifically trust a certain certificate. The hash value is reported if you attempt to connect and the certificate is not trusted.

This technique is similar to certificate pinning and prevents rotating the certificate and blocks man-in-the-middle attacks.

For testing setups you can also use `--accept-any-ssl-certificate` that will disable certificate validation. As this enables various attacks it is not recommended besides for testing.
