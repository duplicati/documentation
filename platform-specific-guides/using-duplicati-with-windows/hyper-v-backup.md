---
description: This page describes how to make backups of Hyper-V machines
---

# Hyper-V backup

On Windows, Duplicati has built-in support for finding Hyper-V machines if running with sufficient privileges. To include a Hyper-V machine in a backup, visit the [Source Data](../../getting-started/set-up-a-backup-in-the-ui.md#id-2.-source-data) step, and find "Hyper-V  Machines" in the treeview:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.19.12.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.18.56.png" alt=""></picture><figcaption></figcaption></figure>

By choosing the top-level "Hyper-V Machines", all Hyper-V machines will be included in the backup. If new machines are added later, these will automatically be included.

## Exclude a VM

If you want to exclude one or more machines, expand the "Hyper-V Machines" entry, and click any VM to toggle its inclusion status:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.19.28.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.20.08.png" alt=""></picture><figcaption></figcaption></figure>

This way of setting it up will mean that new machines will automatically be backed up, but the items you deselected will not be backed up. Note the filters to the right are automatically populated when clicking the VMs. The filter relies on the Hyper-V VMs internal ID, so recreating a VM will usually generate a new ID for the machine.

## Include only specific VMs

If you prefer to manually specify VMs to include so new VMs are not automatically included, make sure the "Hyper-V Machines" element is not selected, then expand the element and click the specific VMs that you want to include in the backup:

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.19.42.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.19.54.png" alt=""></picture><figcaption></figcaption></figure>

This will ensure that new machines will not automatically be included. Notice the "Paths" section on the right is automatically filled with the VMs ID. Beware that recreating the VM will usually cause it to be assigned a new ID.

## Backup of running Hyper-V machines

To support backup of running Hyper-V machines, Duplicati relies on [Volume Shadow Copy Service (VSS)](https://learn.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service). The VSS setup is a series of steps that ensures that the data Duplicati backs up is consistent and can be restored without issues.&#x20;

{% hint style="warning" %}
Using VSS requires the `SE_BACKUP` privilege which often means running Duplicati as Administrator, or as a [Windows Service](run-duplicati-as-a-windows-service.md)
{% endhint %}

In short, the sequence is as follows:

* Duplicati asks for a snapshot
* VSS asks all VSS-aware application to flush to disk
* Applications flush and refrain from updating the disk
* VSS creates a snapshot, and tells all applications to resume
* Anything written to disk while the snapshot is active uses Copy-on-write
* Duplicati reads the consistent snapshot and releases it after the backup ends

This requires a small amount of extra disk and memory to store the data that changes while the snapshot is active, but this is released once the snapshot is released. The end result is that Hyper-V machines can continue to run with no downtime, and Duplicati can read a consistent copy.

To ensure that Hyper-V machines are correctly backed up, change the advanced option `--snapshot-policy` to `Required`:&#x20;

<figure><picture><source srcset="../../.gitbook/assets/Screenshot 2026-06-22 at 11.29.30.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/Screenshot 2026-06-22 at 11.29.18.png" alt=""></picture><figcaption></figcaption></figure>

This setting makes Duplicati abort if it is not able to secure a VSS snapshot.

