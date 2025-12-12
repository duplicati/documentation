---
description: This page describes how to use the AWS S3 storage destination
---

# Amazon S3 destination

The [AWS S3](https://aws.amazon.com/s3/) storage destination is implemented with the [general S3 destination](../standard-based-destinations/s3-compatible-destination.md), so all details from that page applies here as well, but some additional features are supported by AWS.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.31.38 (1).png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.31.26 (1).png" alt="View of the configuration of an S3 bucket"></picture><figcaption></figcaption></figure>

To use an AWS S3 destination you need to fill out: bucket, folder path, server, AWS Access Key Id, AWS Secret Access Key. You can decide on the bucket name and path, and get the Key Id and Access Key from the IAM center.

The server must be set to the region-based server that matches the location where the bucket is created. If you type a non-existing bucket and use the "Test connection" button, Duplicati will ask to create a bucket for you. If you choose "Yes", the bucket will be created in the region you have selected with the advanced options.

## URL format for Commandline

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

### Using Glacier storage class

Since Duplicati stable version 2.2, Duplicati recognizes data in Glacier and will avoid downloading these files for testing. The recommended way to use this is to set up life-cycle rules that move files into cold storage after a period. Once the files are in cold storage, Duplicati will not attempt to read them.

However, if you have retention enabled, you must set `--no-auto-compact` as Duplicati will otherwise attempt to download the files from cold storage, in order to compact them.

Similarly, for a restore, you must manually move files from cold storage into the bucket before attempting the restore operation.
