---
description: This page describes how to use the AWS S3 storage destination
---

# Amazon S3 destination

The [AWS S3](https://aws.amazon.com/s3/) storage destination is implemented with the [general S3 destination](../standard-based-destinations/s3-compatible-destination.md), so all details from that page applies here as well, but some additional features are supported by AWS.

To use the AWS S3 destination, use a format such as:

```
s3://<bucket name>/<prefix>
  ?aws-access-key-id=<account id or username>
  &aws-secret-access-key=<account key or password>
  &s3-location-constraint=<region-id>
```

If you do not supply a hostname, but instead a region, such as `us-east-1`, the hostname will be auto-selected, based on the region. If the region is not supported by the library yet, you can supply the hostname via `--server-name=<hostname>`.

Beware that S3 by default will not use an encrypted connection, and you need to add `--use-ssl=true`to get it working.

## Creating a bucket

When creating a bucket, it will be created in the location supplied by `--s3-location-constraint`. In the case no constraint is supplied, the AWS library will decide what to do. If the bucket already exists, it cannot be created again, so the `--s3-location-constraint` setting will not have any other effect than choosing the hostname.

## Storage class

By default, the objects are created with the "Standard" storage setting, which has optimal access times and redundancy. More information about the different [AWS S3 storage classes](https://aws.amazon.com/s3/storage-classes/) are available from AWS. You can choose the storage class with the option `--s3-storage-class`. Note that you can provide any string here that is supported by your AWS region, despite the UI only offering a few different ones.

### Note on Glacier storage class

Glacier storage does not work well with Duplicati. Because Duplicati really likes to verify that things are working as expected you need to disable these checks. You also need to disable cleanup of data after deleting versions. Restores are tricky, because you need to retrieve data manually from Glacier before Duplicati can work with it.
