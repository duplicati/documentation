---
description: This page describes the iDrive e2 Destination
---

# IDrive e2 Destination

Duplicati supports storing files on [iDrive e2](https://www.idrive.com/s3-storage-e2/), which is a large-scale object storage, similar to S3. In iDrive e2 you store "objects" (similar to files) in "buckets" which define various properties shared between the objects. If you prefix objects with a `/` they can be displayed as virtual folders when listing them.

Note that the bucket id is globally unique, so it is recommended using a name that is not likely to conflict with other users, such as prefixing the bucket with the project id or a similar unique value. If you use a simple name, like `data` or `backup` it is likely already associated with another project and you will get permission errors when attempting to use it.

Note that iDrive has a similar offering called [iDrive Cloud Backup](https://www.idrive.com), which is not currently supported by Duplicati.

To use iDrive e2, you can use the following URL format:

```
e2://<bucket>/<prefix>
  ?access_key_id=<Access key id>
  &access_secret_key=<Access secret key>
```
