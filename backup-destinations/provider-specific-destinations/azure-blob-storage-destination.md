---
description: This page describes the Azure Blob Storage destination
---

# Azure Blob Storage Destination

Duplicati supports backing up to [Azure Blob Storage](https://azure.microsoft.com/en-us/products/storage/blobs), which is a large scale object storage, similar to S3.

## User interface

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2025-11-03 at 15.37.28.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2025-11-03 at 15.37.34.png" alt="Configuring Azure Blob Storage destination"></picture><figcaption></figcaption></figure>

To configure Azure Blob Storage you must fill in: container name, account name and access key. To use a SAS token instead of an access key, use the advanced options.

The **Container name** is the name of the container in the Storage Account.&#x20;

The **Account name** is the name of the Storage Account.&#x20;

The **Access key** can be found on the Storage Account under "Security + Networking" -> "Access keys".  You can use either key1 or key2.

{% hint style="info" %}
Access keys are shared for the Storage Account and the key gives access to all containers in the Storage Account. If you want privilege separation, set up a Service SAS for each backup.
{% endhint %}

If you want to use key rotation, consider using a [secret provider](../../detailed-descriptions/using-the-secret-provider/) and store the keys in the secret provider, so they are always up-to-date.&#x20;

You do not need a separate container for each backup, you can use prefixes to distinguish them, but using individual containers makes it easier to manage rules for each backup.

## URL format for Commandline

To use the Azure Blob Storage destination, you can use the following URL format:

```
azure://<container>/<prefix>
  ?azure-account-name=<account id>
  &azure-access-key=<access key>
```

## Create container

You can create the container via the Azure portal, but if you prefer, you can also let Duplicati create the container for you. The [container names are unique within the storage account and has a number of restrictions](https://learn.microsoft.com/en-us/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names).

If you use the UI, the "Test connection" button will prompt you if the container needs to be created.

## Using a Shared Access Signature (SAS) token

Instead of using a traditional Access Key, you can also use a SAS token. To use this, supply it instead of the access key, for example:

```
azure://<container>/<prefix>
  ?azure-account-name=<account id>
  &azure-access-sas-token=<SAS token>
```

The **account name** is the name of the Storage Account, and the SAS token must have access to read, write, list, and delete files in the container.

## Cold storage

Since Duplicati stable version 2.2, Duplicati recognizes data in cold storage and will avoid downloading these files for testing. The recommended way to use this is to set up life-cycle rules that move files into cold storage after a period. Once the files are in cold storage, Duplicati will not attempt to read them.

However, if you have retention enabled, you must set `--no-auto-compact` as Duplicati will otherwise attempt to download the files from cold storage, in order to compact them.

Similarly, for a restore, you must manually move files from cold storage into the bucket before attempting the restore operation.
