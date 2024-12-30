---
description: >-
  This page describes what a "destination" is to Duplicati and lists some of the
  available providers
---

# Destination overview

Duplicati makes backups of files, called the source, and places the backup data at a destination chosen by the user. To make Duplicati as versatile as possible, each of the destinations are implemented as a "destination" (or "backend"), each with different properties.

Some storage providers support multiple protocols with each their strenghts, and you can generally pick which storage destination provider you like, but if there is a specific implementation for a given storage provider, that is usually the best pick.

Each storage destination has a number of options that can be provided via a URL like format. The options should preferably be provided as part of the URL, but can also be provided via regular commandline options. For instance, the `--use-ssl=true` flag can also be added to the URL with `&use-ssl=true`. If both are provided, the URL value is used.

## Standard based destinations

Destinations in this category are general purpose enough, or commonly used, so they can be used across a range of storage providers. Destinations in this category are:

* [File destination](standard-based-destinations/file-destination.md) (any path in the filesystem)
* [S3-compatible](standard-based-destinations/s3-compatible-destination.md)
* [FTP](standard-based-destinations/ftp-destination.md)
* [SFTP](standard-based-destinations/sftp-ssh-destination.md) (SSH)
* [WebDAV](standard-based-destinations/webdav-destination.md)
* [OpenStack](standard-based-destinations/openstack-destination.md)
* [Rclone](standard-based-destinations/rclone-destination.md) (binary required)

## Provider specific destinations

Storage destinations in this category are specific to one particular provider and implemented using either their public API description, or by using libraries implemented for that provider. Destinations in this category are:

* [Backblaze B2](provider-specific-destinations/backblaze-b2-destination.md)
* [Box.com](provider-specific-destinations/box.com-destination.md)
* [Rackspace CloudFiles](provider-specific-destinations/rackspace-cloudfiles-destination.md)
* [Mega.nz](provider-specific-destinations/mega.nz-destination.md)
* [Aliyun OSS](provider-specific-destinations/aliyun-oss-destination.md)
* [Tencent COS](provider-specific-destinations/tencent-cos-destination.md)
* [Jottacloud](provider-specific-destinations/jottacloud-destination.md)
* [pCloud](provider-specific-destinations/pcloud-destination.md)
* [Azure Blob Storage](provider-specific-destinations/azure-blob-storage-destination.md)
* [Google Cloud Storage](provider-specific-destinations/google-cloud-storage-destination.md)
* [Microsoft Group Drive](provider-specific-destinations/microsoft-group-destination.md)
* [SharePoint](provider-specific-destinations/sharepoint-destination.md)
* [Amazon S3](provider-specific-destinations/amazon-s3-destination.md)

## File synchronization providers

Storage destinations in this category are also specific to one particular provider, but these storage provider products are generally intended to be used as file synchronization storage. When they are used with Duplicati, the backup files will generally be visible as part of the synchronization files. Destinations in this category are:

* [Dropbox](file-synchronization-providers/dropbox-destination.md)
* [GoogleDrive](file-synchronization-providers/googledrive-destination.md)
* [OneDrive](file-synchronization-providers/onedrive-destination.md)
* [OneDrive for business](file-synchronization-providers/onedrive-for-business-destination.md)

## Decentralized providers

Storage destinations in this category are utilizing a decentralized storage strategy and requires knowledge about each system to have it working. Some of these may require additional servers or intermediary providers and may have different speed characteristics, compared to other storage providers. Destinations in this category are:

* [Sia](decentralized-providers/sia-destination.md)
* [Storj](decentralized-providers/storj-destination.md) (aka Tardigrade)
* [TahoeLAFS](decentralized-providers/tahoelafs-destination.md)
