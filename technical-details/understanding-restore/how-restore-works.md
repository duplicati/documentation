---
description: This page describes how the restore process works from a high level
---

# How Restore Works

The core restore process is from a high level perspective:

* Figure out what files should be restored
* For each file
  * Locate each block required
  * For each block
    * Locate the block volume
    * Download block volume
    * Decrypt and decompress block
    * Append block to file
  * After file is restored, verify the content matches the original

This description is a bit simplified, as it would be incredibly slow to execute in the basic form shown here. For a more developer oriented description, see the forum post on [how the restore process works](https://forum.duplicati.com/t/how-the-restore-process-works/17684).

The part about "figuring out what files should be restored" can be described in more parts:

* Find the full list of files in the version to restore from
* Apply filters to only restore the requested files
* Skip files that exists and contains the correct content

If there is a local database, many of the steps can be reduced to a database query, and for this reason, Duplicati will build a temporary database during restore, if there is not already a local database.
