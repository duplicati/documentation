---
description: This page describes the Storj storage destination
---

# Storj Destination

Duplicati supports backups to the [Storj network](https://www.storj.io) which is a large-scale decentralized storage network. The destination supports two different ways of authenticating: Access Grant and Satellite API.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 16.50.48.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 16.50.39.png" alt="Configure the Storj destination"></picture><figcaption></figcaption></figure>

To configure the Storj destination, choose the satellite that you will connect to, then provide the access grant. The bucket and path can be used to control where the data is stored within the network.

## URL format for Commandline

### Access Grant

To use the access grant method, use the following URL format:

```
storj://
  ?storj-auth-method=Access%20Grant
  &storj-shared-access=<access key>
```

### Satellite API

To use a satellite API, use the following URL format:

```
stor://
  ?storj-satellite=<hostname:port>
  &storj-api-key=<api key>
  &storj-secret=<secret>
```

If the `--storj-satellite` is omitted it will default to a US based endpoint.

## Bucket and folder

To choose the bucket where data is stored, use the `--storj-bucket` which will default to `duplicati`. If further differentiation is needed, use `--storj-folder` to specifiy a folder within the bucket where data is stored.
