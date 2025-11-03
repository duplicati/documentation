---
description: This page describes the Google Cloud Storage destination
---

# Google Cloud Storage Destination

Duplicati supports storing files on [Google Cloud Storage](https://cloud.google.com/storage), aka GCS, which is a large-scale object storage, similar to S3. In GCS you store "objects" (similar to files) in "buckets" which define various properties shared between the objects. If you use a `/` in the object prefix, they can be displayed as virtual folders when listing them.

Note that the bucket id is globally unique, so it is recommended using a name that is not likely to conflict with other users, such as prefixing the bucket with the project id or a similar unique value. If you use a simple name, like `data` or `backup` it is likely already associated with another project and you will get permission errors when attempting to use it.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.38.07.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.37.59.png" alt="Configuring Google Cloud Storage destination"></picture><figcaption></figcaption></figure>

To configure the Google Cloud Storage destination you need to supply the bucket name, then pick a unique folder name for the backups, and then authorize Duplicati to work on your behalf. Simply click the "AuthID" link in the text field and the authentication process will start and fill out the "AuthID" when you are done.

If you use the "Test connection" button and the bucket does not exist, Duplicati will offer to create the bucket, using the parameters set in advanced options.

## URL format for Commandline

To use GCS, you can use the following URL format:

```
gcs://<bucket>/<prefix>?authid=<authid>
```

To use Google Cloud Storage you must first obtain an `AuthID` by using a Duplicati service to log in to Google and approve the access. See the [page on the OAuth Server](../../duplicati-programs/oauth-server.md) for different ways to obtain an AuthID.

## Using service token

Because the OAuth token has full access to the Google Account, it is recommended that you use  a Google [Service Accounts](https://cloud.google.com/iam/docs/keys-create-delete) instead. Once you have obtained the Service Account JSON, provide it with either `--gcs-service-account-file=/path/to/jsom`  or `--gcs-service-account-json=<urlencoded json>`. If you are using the graphical user interface, you can paste the JSON into the advanced option area:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.38.19.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.38.26.png" alt="Entering Service Account JSON"></picture><figcaption></figcaption></figure>

## Creating a bucket

You can [create a bucket from within the Google Cloud Console](https://cloud.google.com/storage/docs/creating-buckets#console) and here you can set all options as desired. If you prefer to let Duplicati create the bucket, you can also set the parameters from Duplicati.

You set the project the bucket belongs to with `--gcs-project=<project id>`  and the desired location with `--gcs-location=<location>`. You can get the project id from the Google Cloud Console and [see the possible GCS bucket locations in the GCS documentation](https://cloud.google.com/storage/docs/locations).

When creating the bucket you can also choose the storage class with `--gcs-storage-class`. You can choose any of the [storage class values shown in the GCS documentation](https://cloud.google.com/storage/docs/storage-classes), even if they are not reported as possible by Duplicati.

These options have no effect if the bucket is already created.
