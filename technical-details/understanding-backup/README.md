---
description: This page describes the backup process and goals
---

# Understanding Backup

When running a backup, the goal is to create a new "snapshot" of the filesystem. In some cases the operating system is capable of providing a consistent disk-level snapshot, but if not, the goal is to get as close to a snapshot as possible. Because it is not guaranteed that a true disk-level snapshot was made, Duplicati refers to these "snapshots" as versions.

Each backup will produce a "filelist" (aka `.dlist`) file on the remote storage as well as one or more block volumes (aka `.dblock`) and usually also one or more index volumes (aka `.dindex`). The filelists explain how to recreate the files that are in this particular version, and the block volumes contains the actual file contents, split into blocks.

The index volumes are optional, and not required for restores, but are created by default as they reduce the amount of data that needs to be processed if the [local database](../../detailed-descriptions/the-local-database.md) is lost.
