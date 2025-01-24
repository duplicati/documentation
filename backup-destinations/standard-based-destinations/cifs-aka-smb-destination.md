---
description: This page describes the CIFS storage destination
---

# CIFS (aka SMB) Destination

The Common Internet File System (CIFS) backend provides native support for accessing shared network resources using the CIFS/SMB protocol. This backend enables direct interaction with Windows shares and other CIFS-compatible network storage systems.

To use the CIFS destination, you can use a url such as:

```
cifs://<hostname>/<share>/<path>
  ?auth-username=<username>
  &auth-password=<password>
  &transport=directtcp
```

## Transport

CIFS supports two distinct transport protocols, each with its own characteristics:



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

\--[**write-buffer-size**](#user-content-fn-2)[^2]

Defines the write buffer size, in bytes, for SMB operations (Will be capped automatically by SMB negotiated values, values bellow 10000 bytes will be ignored)



CIFS Backend is available on Canary release from [v2.1.0.106](https://github.com/duplicati/duplicati/releases/tag/v2.1.0.106_canary_2025-01-11)&#x20;

[^1]: Manual buffer size adjustment is typically unnecessary and not recommended as it may interfere with the protocol's built-in optimization. The default negotiation process ensures optimal performance for most use cases.

[^2]: Manual buffer size adjustment is typically unnecessary and not recommended as it may interfere with the protocol's built-in optimization. The default negotiation process ensures optimal performance for most use cases.
