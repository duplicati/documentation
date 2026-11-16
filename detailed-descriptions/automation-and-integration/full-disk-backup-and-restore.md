---
description: >-
  This page describes how to back up and restore full disks and individual
  partitions with Duplicati
---

# Full-disk backup and restore

Duplicati supports backing up an entire disk, or individual partitions on a disk, as the backup source. Unlike a regular file-based backup, a full-disk backup reads the disk directly and also captures the disk geometry, such as the partition table and boot information. This makes it possible to restore the backup onto a blank replacement disk and end up with a bootable system, without reinstalling the operating system first.

**Key characteristics**

* **Disk-level source**: Back up a complete disk, including the partition layout, or pick just the partitions you need.
* **Flexible restore**: Restore disk-to-disk, partition-to-partition, or a partition from a full-disk backup onto another partition.

{% hint style="warning" %}
Reading raw disks requires elevated privileges. Run Duplicati as a service or with administrator/root permissions, otherwise the backup or restore will fail.
{% endhint %}

## Setting up a full-disk backup

To create a full-disk backup, [set up a backup following the regular steps](../../getting-started/set-up-a-backup-in-the-ui.md). On the "Source Data" page, click the "Add local disk" button to browse local disks:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-08-22 at 08.12.13.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-08-22 at 08.11.46.png" alt=""></picture><figcaption><p>Choosing a disk in the full-disk source picker</p></figcaption></figure>

Selecting the disk itself backs up the entire disk, including the partition table and all partitions. Selecting a single partition backs up only that partition, which is functionally the same as selecting the full disk and filtering out the unwanted partitions, but more intuitive when only one partition is needed.

Once a disk has been adedd, it will show up in the source tree, where you can expand it to see available partitions:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-08-22 at 08.12.40.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-08-22 at 08.12.48.png" alt=""></picture><figcaption><p>Selecting a disk or partition as the backup source</p></figcaption></figure>

You can click the individual partitions to toggle their inclusion, if you only want specific partitions.

After choosing the source, continue with the schedule and retention settings as for a regular backup.

## Restoring a full-disk backup

A full-disk backup is restored through the regular [restore flow](../../getting-started/restoring-files.md), either from the existing backup configuration or directly from the backup files.

When choosing what to restore, the backup version lists the disk with its partitions. Select the disk to restore everything, or expand it and select only the partitions you need:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-08-22 at 08.14.47.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-08-22 at 08.14.39.png" alt=""></picture><figcaption><p>Choosing a disk or partition to restore</p></figcaption></figure>

On the "Restore options" page, choose where the restored data should go:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-08-22 at 08.15.08.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-08-22 at 08.15.18.png" alt=""></picture><figcaption><p>Choosing the restore target</p></figcaption></figure>

* **Target disk**: Writes the backup back to a physical disk or partition. Pick the target from the tree:
  * Restoring a disk to a disk recreates the partition table and restores the partitions, producing a bootable disk.
  * Restoring a partition to a partition writes only that partition, which is useful when the disk contains multiple partitions and only one should be restored.
  * Restoring a partition to a disk creates a new partition on the target disk as needed.
* **Image file**: Writes the disk into a disk image file instead of a physical disk, for use with a virtual machine or other disk tools, or to mount it in the operating system. Choose the folder and the file name, and the format: a raw image (`.img`), or a dynamic VHD (`.vhd`) or VHDX (`.vhdx`) file. Check "Replace the image file if it exists" to overwrite an existing file. This target is available from canary 2.4.1.101.
* **Local file system**: Extracts the sectors from the disk backup to a regular folder. This is useful for forensics or for disk imaging tools.

{% hint style="warning" %}
Restoring to a target disk or partition **overwrites its contents**. Double-check that you have selected the correct target, and make sure the target is at least as large as the source. You cannot restore over the disk that the running operating system is on, so a full system restore is usually performed by booting from recovery media or by attaching the target disk to another machine.
{% endhint %}

When satisfied with the settings, click "Submit" and the restore process will write the data to the selected target.

### Restoring to an image file

A raw image is created as a sparse file, so regions that contain only zeros do not use disk space. To reserve the full size of the image up front instead, check "Reserve the full size of the image file", or set `--diskimage-restore-image-preallocate=true`. VHD and VHDX files only store the used parts of the disk.

On the command line, `--diskimage-restore-image-format` chooses the format: `raw`, `vhd`, `vhdx`, or `auto`, the default. With `auto`, a new image is written as VHD or VHDX when the file name ends in `.vhd` or `.vhdx`, and as a raw image otherwise; an existing image keeps its format.

To restore a single partition into an image file, the image file must already exist and contain that partition. Restoring to an image file is not supported with `--restore-legacy` or `--restore-all-files`.
