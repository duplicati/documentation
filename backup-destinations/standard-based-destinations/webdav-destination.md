---
description: This page describes the WebDAV storage destination
---

# WebDAV Destination

The WebDAV protocol is a minor extension to the HTTP protocol used for web requests. Because it is compatible with HTTP it also supports SSL/TLS certificates and verification similar to what websites are using.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.55.28.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.55.21.png" alt="Showing the options needed for configuring the WebDAV connection"></picture><figcaption></figcaption></figure>

To use the WebDAV destination you must enter: server, path on server, username and password. Depending on your setup, you may also need to add some advanced options as explained below.

## URL format for Commandline

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

To use an encrypted connection, add the option `--use-ssl=true` such as:

```
webdav://<hostname>/<path>
  ?auth-username=<username>
  &auth-password=<password>
  &use-ssl=true
```

This will then use an HTTPS secured connection subject to the operating system certificate validation rules. If you need to use a self-signed certificate that is not trusted by the operating system, you can use the option `--accept-specified-ssl-hash=<hash>` to specifically trust a certain certificate. The hash value is reported if you attempt to connect and the certificate is not trusted.

This technique is similar to certificate pinning and prevents rotating the certificate and blocks man-in-the-middle attacks. If you are using the graphical user interface, the "Test connection" button will detect untrusted certificates and ask to pin the certificate.

For testing setups you can also use `--accept-any-ssl-certificate` that will disable certificate validation. As this enables various attacks it is not recommended besides for testing.

If the revocation status of the certificate cannot be checked, for instance because the revocation server is offline, the option `--ignore-revocation-failure` ignores that failure. This option is available from Duplicati 2.4.0.0.

## Listing files

Duplicati lists the files on the server with a WebDAV `PROPFIND` request. If the listing fails or returns wrong file names, these options can help:

* `--use-extended-propfind=true` sends a request body that explicitly asks for all properties, instead of an empty body. This is required for some servers.
* `--use-legacy-parsing=true` uses the older logic to read the file names from the response. The current logic already falls back to the older logic for names it cannot read, so this is only needed if the current logic returns wrong names.
* `--debug-propfind-file=<path>` writes the response from the server to the given file, which is useful when reporting a problem with the listing. The file is overwritten on each listing.
