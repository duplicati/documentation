---
description: This page describes the backup process and goals
---

# Understanding Backup

When running a backup, the goal is to create a new "snapshot" of the filesystem. In some cases the operating system is capable of providing a consistent disk-level snapshot, but if not, the goal is to get as close to a snapshot as possible. Because it is not guaranteed that a true disk-level snapshot was made, Duplicati refers to these "snapshots" as versions.
