---
description: This page describes the TahoeLAFS storage destination
---

# TahoeLAFS destination

Duplicati supports backups to the [Tahoe least-authority file store](https://github.com/tahoe-lafs/tahoe-lafs), Tahoe-LAFS.&#x20;

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 16.49.49.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 16.49.58.png" alt="Configure the Tahoe LAFS destination"></picture><figcaption></figcaption></figure>

To configure the TahoeLAFS destination, enter the server and the path.

## URL format for Commandline

To use the TahoeLAFS destination, use this URL format:

```
tahoe://<hostname>:<port>/uri/URI:DIR2:<folder>
  ?use-ssl=true
```
