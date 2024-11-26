---
description: This page describes the overall design goals for the Duplicati client
---

# Architecture Premises

At the core, Duplicati is designed to make a "copy" of files on a machine, and later be able to restore these files as they looked at the time they were "copied".&#x20;

Most data to be "copied" is expected to change slowly, such that each new "copy" contains add/delete/modifications applied to various files, but overall contains a significant part that is unchanged from last "copy".

The best storage destination is a choice that depends on multiple factors which can change over time. And to cater to these changes in storage solutions, new solutions may appear, having unique properties.

Since the storage destination may be remote, it is assumed that the transfer link can be slow and have random errors. It is also assumed that the remote destination is not fully infallible.

It is also assumed that the remote destination is not owned by the entity making the backup, so confidentiality of stored files cannot be guaranteed by the storage provider.

## Overall design

With the above assumptions and observations, the core of Duplicati was designed with specfic choices:

* Each backup "copy" should a snapshot of the system
* Files are treated as a stream of blocks
* Data is broken into fixed-size blocks
* Blocks are only stored once to reduce storage space (deduplication)
* Blocks are combined into volumes to reduce the number of remote storage requests
* Volumes are compressed to save storage space (compression)
* Volumes are encrypted to ensure integrity and confidentiality (encryption)
* No storage destination server/service is required (client-only)

These choices are the foundation from which the rest of Duplicati is built. With the use of client-side encryption, Duplicati can be classified as a [Trust-no-one / Zero-Trust](https://en.wikipedia.org/wiki/Zero_trust_architecture) system, where the possesion of the encryption key decides who has access to the data.

To ensure that Duplicati can work with different storage destinations, such as S3 and a network share, any interaction with the storage destination is limited to 4 commands: GET, PUT, LIST, and DELETE. Any storage system that supports these 4 commands can work with Duplicati.

The deduplication and block-level tracking makes it possible to avoid the traditional full+incremental cycle, where the full backup is always required to get to a specific version.

To ensure the remote storage is consistent, Duplicati uses a local database, where all files, blocks, volumes, etc. are tracked. This database makes it possible to detect remote storage problems early, and makes it possible to run backups without downloading any volumes from the remote storage.

