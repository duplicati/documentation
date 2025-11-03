---
description: This page describes the Jottacloud storage destination
---

# Jottacloud Destination

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.35.06.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.35.16.png" alt="Jottacloud destination configuration"></picture><figcaption></figcaption></figure>

To configure the Jottacloud destination you need to pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

## URL format for Commandline

To use the [Jottacloud](https://jottacloud.com/) storage destination, you can use the following URL format:

```
jottacloud://<folder>/<subfolder>
  ?authid=<authid>
```

To use Jottacloud you must first obtain an `AuthID` by using a Duplicati service to log in to Jottacloud and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Device and mount point

Within Jottacloud, each machine registered is a device that can be used for storage, and within each device you an choose the mount point. By default, Duplicati will use the special device `Jotta` and the mount point `Archive`.&#x20;

If you need to store data on another device, you can use the options `--jottacloud-device` and `--jottacloud-mountpoint` to set the device and mount point. If you only set the device, the mount point will be set to `Duplicati`.

## Performance tuning

If you need to tune the performance and resource usage to match your specific setup, you can adjust the two parameters:

* `--jottacloud-threads`: The number of threads used to fetch chunks with
* `--jottacloud-chunksize`: The size of chunks to download with each thread
