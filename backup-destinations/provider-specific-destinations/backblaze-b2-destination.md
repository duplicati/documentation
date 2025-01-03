---
description: This page describes the Backblaze B2 storage destination
---

# Backblaze B2 Destination

Duplicati supports storing files with Backblaze B2, which is a large-scale object storage, similar to S3. With B2 you store "objects" (similar to files) in "containers" which define various properties shared between the objects. If you use a `/` in the object prefix, they can be displayed as virtual folders when listing them.

To use the B2 storage destination, use the following URL format:

```
b2://<bucket>/<prefix>
  ?b2-accountid=<account id>
  &b2-applicationkey=<application key>
```

## Create a bucket

You can use the Backblaze UI to create your buckets, but if you need to create buckets with Duplicati, this is also possible. The default is to create private buckets, but you can create public buckets with `--b2-create-bucket-type=allPublic`.&#x20;

## Performance tuning

You can change the size of file listings to better match pricing and speed through `--b2-page-size`, which is default set to 500, meaning you will have a list request for each 500 objects. Note that setting this higher may cause the number of requests to go down, but each requests may be priced as a more expensive request.

If you prefer downloads from you custom domain name, you can supply this with `--b2-download-url`. This setting does not affect uploads.
