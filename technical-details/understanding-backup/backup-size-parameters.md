---
description: This page describes the backup size parameters that used in Duplicati
---

# Backup size parameters

Duplicati has a wide range of options that can be used to tweak the default behavior. As a rule, all parameters have a default value that fits most users, but it may increase performance to tweak some parameters. The two specific parameters for size have a visible and significant impact, and they are described here. A [more extensive explanation is provided on the forum](https://forum.duplicati.com/t/choosing-sizes-in-duplicati/17683).

## Block size

As mentioned in the section on [how backup works](how-backup-works.md), the files are split into a sequence of blocks. The block size parameter defines how large these blocks are. Using a small block size increases the chance that blocks are deduplicated and reduces the amount of data to upload if a file has changed.

On the other hand, a small block size increases the number of blocks to keep track of, which may reduce overall performance.

Typically, deduplication happens within identical files (file copies) or within large files, where parts are updated. This means the gain from a small block size is unlikely to increase deduplication, but it does decrease the amount of data to store after a change (since changes less than a block cannot be expressed).

A typical value for the block size is between 100KiB for backups less than 3 GiB, and up to 1MiB.&#x20;

Importantly, since the block size is fixed, it is not possible to change it after running the initial backup. This is because there is no way to compare blocks of different sizes, so it would essentially be a new backup if the size was changed.

## Remote volume size

The remote volume size defines what size files are restored on the remote destination. When changing the remote volume size, it directly affects the number of files stored on the remote destination. Ignoring deduplication and compression, you can roughly estimate the number of files by dividing the source sixe with the remote volume size.

Having a large volume size decreases the overhead associated with each file, such as headers, initial transfer handshake, smaller lists, etc. However, as described in the section on [how restore works](../understanding-restore/how-restore-works.md), it is required that a full remote volume is downloaded to restore even a small file. If your use case requires restoring multiple small files, they may be scattered across multiple remote volumes, causing a large download and processing overhead.

The optimal size depends both on the source size, the need to restore small files, and the network speed. If you are using a cloud-based provider, the speed is usually limited by your connection speed, and here a reasonable volume size is around 50-500 MiB. If you are on a local network, it may be acceptable to use a remote volume size in the range of 500-2000MiB.
