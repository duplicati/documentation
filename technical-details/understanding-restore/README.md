---
description: This page describes the restore process and goals
---

# Understanding Restore

For the restore process, the goal is to recreate the files in a "snapshot", with the contents as they were at the time the backup was made. To achieve this, the restore process first obtains a list of files, and from that builds a list of blocks that are required to recreate these files.

If a [local database](../../detailed-descriptions/the-local-database.md) is present, this information can be extracted directly from the database, and only the actual blocks are retrieved from the remote storage. If a local database is not present, a partial local database is created by downloading the relevant file list (aka `.dlist`) and index files (aka `.dindex`). If the index files are missing or do not contain the required information, Duplicati will download the block volumes (aka `.dblock`) files until all information is obtained.

The restore process will perform a best-effort restore if some of the data is missing, and give an error message at the end, explaining what went wrong. The goal with this is to be as fault tolerant as possible, but not give the illusion that the restore was succesful if it was not.

At the end of the restore, all restored files are verified to ensure they were restored correctly.
