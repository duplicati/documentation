---
description: >-
  This page describes the Alibaba Cloud Object Storage Service, also known as
  Aliyun OSS.
---

# Aliyun OSS Destination

Duplicati supports storing files on [Alibaba Cloud Object Storage Service](https://www.alibabacloud.com/en/product/object-storage-service), aka Aliyun OSS, which is a large-scale object storage, similar to S3. In Aliyun OSS you store "objects" (similar to files) in "buckets" which define various properties shared between the objects. If you use a `/` in the object prefix, they can be displayed as virtual folders when listing them.

Note that the bucket id is globally unique, so it is recommended using a name that is not likely to conflict with other users, such as prefixing the bucket with the project id or a similar unique value. If you use a simple name, like `data` or `backup` it is likely already associated with another project and you will get permission errors when attempting to use it.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.33.56.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.34.03.png" alt="Aliyun OSS destination configuration"></picture><figcaption></figcaption></figure>

To use the Aliyun OSS destination you must specify: bucket, path in bucket, access key id, access key secret, and endpoint.

## URL format for Commandline

To use Aliyun OSS, you can use the following URL format:

```
aliyunoss://<prefix>
  ?oss-bucket=<Bucket name>
  &oss-endpoint=<Endpoint>
  &oss-access-key-id=<Access Key Id>
  &oos-access-key-secret=<Access Key Secret>
```

The [endpoint is defined by Aliyun](https://www.alibabacloud.com/help/en/oss/user-guide/regions-and-endpoints) and needs to match the region the bucket is created it. The access key can be obtained or created in the Cloud Console.
