---
description: This page describes the S3 storage destination
---

# S3-compatible Destination

The Simple Storage Service, S3, was originally described, developed and offered by Amazon via AWS. Since then, numerous other providers have adopted the protocol and offer S3-compatible services. While these services are mostly compatible with the core S3 protocol, a number of additional AWS-specific settings are usually not supported and will be ignored.

This page deals with S3 in general, for a specific [setup on AWS S3, refer to the AWS specific page](../provider-specific-destinations/amazon-s3-destination.md).

When storing data in S3, the storage is divided into a top-level "folder" called a "bucket", and each bucket has "objects", similar to files. For most providers, an object name with `/`characters will be interpreted as subfolders in some way.

In the original S3 specification, the bucket name was used as part of the hostname, causing some issues with bucket names that are not valid hostnames, and some delays for new buckets caused by DNS update speeds. Newer solutions use a single shared hostname and provide the bucket name as a parameter.

For AWS S3, and most other providers, the bucket name is a global name, shared across all users. This means that simple names, such as `backup` or `data` will likely be taken, and attempts to use these will cause permission errors. For [AWS, the recommendation is to use a guid in the bucket name](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html#automatically-created-buckets) to make it unique. The Duplicati UI will recommend prefixing the account id to the bucket name, to make it unique.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.31.38 (1).png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.31.26 (1).png" alt="The S3 Backend Configuration view"></picture><figcaption></figcaption></figure>

To use the S3 backend you must fill in details for all the fields: bucket, folder path, server, AWS Access Key ID, and AWS Secret Access Key. Not that your provider may use different names for the different values, especially the Access Key Id and Secret Access Key may be called something like username and password.

With the advanced options you can choose many extra settings as described below.

## URL format for Commandline

To use S3 as the storage destination, us a format such as:

```
s3://<bucket name>/<prefix>
  ?aws-access-key-id=<account id or username>
  &aws-secret-access-key=<account key or password>
  &s3-servername=<server ip or hostname>
  &use-ssl=true
```

Note that the default for S3 is to use unencrypted connections. The connections are secured with signatures, but all data transfered can be captured through the network. If the provider supports SSL/TLS, which most do, make sure to add `--use-ssl=true`to also encrypt the connection.

Make sure you consult the provider documentation to get the server name you need for the bucket region. If you are using AWS, [see the AWS S3 description](../provider-specific-destinations/amazon-s3-destination.md).

## Choosing the client

The S3 storage destination can either use the [AWS S3 library](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/csharp_s3_code_examples.html) or [Minio library](https://min.io/docs/minio/linux/developers/dotnet/minio-dotnet.html), and you can choose the library to use with `--s3-client=minio`.

Generally, both libraries will work with most providers, but the AWS library has some defaults that may not be compatible with other providers. While you can configure the settings, it may be simpler to use Minio with the default settings.

## Using non-AWS storage

Most providers other than AWS S3 use an older version of the protocol, so to connect to them you often need to set either the option `--s3-disable-chunk-encoding` or use the Minio client with `--s3-client=minio` (but not both):

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 14.36.22.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 14.36.28.png" alt="Advanced options picked for compatibility with non-AWS storage"></picture><figcaption></figcaption></figure>

## Creating the bucket

Since the bucket defines the place where data is stored, a bucket needs to be created before it can be used. All providers will offer a way to do this through their UI, and allows you to set various options, such as which geographical region the bucket is located in.

If you use Duplicati to create the bucket, you can also set the option `--s3-location-contraint`to provide the desired location. Support for this, and available regions, depends on the provider.&#x20;

## Storage class

With S3 it is also possible to set the storage class which is sometimes used to fine-tune the cost/performance/durability of the files. The storage class is set with `--s3-storage-class`, but the possible settings depends on the provider.
