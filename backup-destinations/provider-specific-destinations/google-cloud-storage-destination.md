---
description: This page describes the Google Cloud Storage destination
---

# Google Cloud Storage Destination

Duplicati supports storing files on [Google Cloud Storage](https://cloud.google.com/storage), aka GCS, which is a large-scale object storage, similar to S3. In GCS you store "objects" (similar to files) in "buckets" which define various properties shared between the objects. If you prefix objects with a `/` they can be displayed as virtual folders when listing them.

Note that the bucket id is globally unique, so it is recommended using a name that is not likely to conflict with other users, such as prefixing the bucket with the project id or a similar unique value. If you use a simple name, like `data` or `backup` it is likely already associated with another project and you will get permission errors when attempting to use it.

To use GCS, you can use the following URL format:

```
gcs://<bucket>/<prefix>?authid=<authid>
```

## Creating a bucket

You can [create a bucket from within the Google Cloud Console](https://cloud.google.com/storage/docs/creating-buckets#console) and here you can set all options as desired. If you prefer to let Duplicati create the bucket, you can also set the parameters from Duplicati.

You set the project the bucket belongs to with `--gcs-project=<project id>`  and the desired location with `--gcs-location=<location>`. You can get the project id from the Google Cloud Console and [see the possible GCS bucket locations in the GCS documentation](https://cloud.google.com/storage/docs/locations).

When creating the bucket you can also choose the storage class with `--gcs-storage-class`. You can choose any of the [storage class values shown in the GCS documentation](https://cloud.google.com/storage/docs/storage-classes), even if they are not reported as possible by Duplicati.

These options have no effect if the bucket is already created.
