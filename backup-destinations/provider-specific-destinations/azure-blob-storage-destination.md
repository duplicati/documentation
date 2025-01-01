---
description: This page describes the Azure Blob Storage destination
---

# Azure Blob Storage Destination

Duplicati supports backing up to [Azure Blob Storage](https://azure.microsoft.com/en-us/products/storage/blobs), which is a large scale object storage, similar to S3.

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
