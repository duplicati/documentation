---
description: This page describes how backups work from a high level perspective
---

# How Backup Works

The core backup process is at a high level view:

* Activate operating system snapshot (if possible)
* Scan source folder content recursively
* For each file
  * Read a block at a time
  * Check if the block already exists
    * Ignore if it exists
    * Add to volume if new
  * Process next block

With this core, it is possible to generate the volumes that are uploaded. Each time a block is added to a volume, the size is checked against the maximum volume size. If the limit is reached, a new volume is started, and the previous volume is processed. For a more detailed developer-oriented description, see the [forum post on how the backup process works](https://forum.duplicati.com/t/how-the-backup-process-works/17682).

The process for a volume file is:

* Compress file (if not done when adding a block)
* Encrypt the file
* Upload the file

At the end of the backup, when all files are processed, and all blocks are uploaded, a file list is uploaded that explains how to recreate all the processed files.

After the upload, a check is made to ensure that the remote storage looks as expected, and a few random volumes are downloaded to ensure the backup data is available.

If there are any retention rules active, Duplicati will then remove old lists, and possibly start to reclaim some remote storage by consolidating remote volumes.&#x20;
