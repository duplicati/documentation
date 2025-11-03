---
description: This page describes the CIFS storage destination
---

# SMB (aka CIFS) Destination

The Server Message Block (SMB) / Common Internet File System (CIFS) backend provides native support for accessing shared network resources using the SMB/CIFS protocol. This backend enables direct interaction with Windows shares and other SMB-compatible network storage systems.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.09.29.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.09.37.png" alt="View of the SMB configuration page"></picture><figcaption></figcaption></figure>

To use the SMB connection you must supply the server, shared name, path on server, username, password, and transport. See below for a description of the transport method.

## URL format for Commandline

To use the CIFS destination, you can use a url such as:

```
smb://<hostname>/<share>/<path>
  ?auth-username=<username>
  &auth-password=<password>
  &transport=directtcp
```

## Transport

SMB supports two distinct transport protocols, each with its own characteristics:



**DirectTCP (**&#x64;irecttcp)

* **Port**: 445
* **Characteristics**:
  * Faster performance
  * Modern implementation
  * Preferred for newer systems
  * Direct TCP/IP connection
  * Lower overhead

#### NetBIOS over TCP (netbios)

* **Port**: 139
* **Characteristics**:
  * Legacy support
  * Compatible with older systems
  * Additional protocol overhead
  * Slower performance
  * Uses NetBIOS naming service

## Advanced Options

\--[**read-buffer-size**](#user-content-fn-1)[^1]

Defines the read buffer size, in bytes, for SMB operations (Will be capped automatically by SMB negotiated values, values bellow 10000 bytes will be ignored)

\--[**write-buffer-size**](#user-content-fn-1)[^1]

Defines the write buffer size, in bytes, for SMB operations (Will be capped automatically by SMB negotiated values, values bellow 10000 bytes will be ignored)

The SMB backend is available on stable release from version 2.2.

[^1]: Manual buffer size adjustment is typically unnecessary and not recommended as it may interfere with the protocol's built-in optimization. The default negotiation process ensures optimal performance for most use cases.
