---
description: This page describes the Storj storage destination
---

# Storj Destination

Duplicati supports backups to the [Storj network](https://www.storj.io) which is a large-scale decentralized storage network. The destination supports two different ways of authenticating: Access Grant and Satellite API.

## Access Grant

To use the access grant method, use the following URL format:

```
storj://
  ?storj-auth-method=Access%20Grant
  &storj-shared-access=<access key>
```

## Satellite API

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
