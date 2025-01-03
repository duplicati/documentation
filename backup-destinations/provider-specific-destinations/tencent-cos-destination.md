---
description: This page describes the Tencent COS storage destination
---

# Tencent COS Destination

Duplicati supports storing files on [Tencent Cloud Object Storage (COS)](https://www.tencentcloud.com/products/cos) which is a large-scale object storage, similar to S3. In Tencent COS you store "objects" (similar to files) in "buckets" which define various properties shared between the objects. If you prefix objects with a `/` they can be displayed as virtual folders when listing them.

To use GCS, you can use the following URL format:

```
cos://<prefix>
  ?cos-bucket=<Bucket name>
  &cos-region=<Bucket region>
  &cos-app-id=<Account AppId>
  &cos-secret-id=<API Secret Id>
  &cos-secret-key=<API Secret Key>
```

The bucket name is user-chosen, and the region must match the [bucket region](https://www.tencentcloud.com/document/product/436/6224). The remaining values can be obtained from the Cloud Console.

Note that the bucket must be created from within the Cloud Console prior to use.

## Storage class

The objects uploaded can be in different [storage classes](https://www.tencentcloud.com/document/product/436/30925), which can be set with `--cos-storage-class`.

**NOTE:** The `ARCHIVE` and  `DEEP_ARCHIVE` storage does not work well with Duplicati. Because Duplicati really likes to verify that things are working as expected you need to disable these checks. You also need to disable cleanup of data after deleting versions. Restores are tricky, because you need to manually restore data to the standard storage class before Duplicati can access it.



