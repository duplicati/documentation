---
description: This page describes how to use the Duplicati Snapshots tool
---

# Snapshots

The Snapshots tool is intended to test the system snapshot capability, and will invoke the same system calls as Duplicati to set up and tear down a system snapshot.

The Snapshots tool is called `Duplicati.CommandLine.Snapshots.exe` on Windows and `duplicati-snapshots` on Linux and MacOS.

To run the tool, invoke it with a folder to use for testing. To work correctly the folder should be on the filesystem/disk/volume/etc that will be part of the snapshot:

```
duplicati-snapshots <path to test folder>
```

The tool will do the following:

1. Create the folder if it does not exist
2. Place a file named `testfile.bin` inside the folder
3. Lock the file
4. Verify that the file is locked
5. Create a snapshot containing the folder
6. Check that the file can be read from the snapshot
7. Tear down the snapshot

On **Windows**, this will use VSS to create snapshots, which require elevated privileges, usually Administrator.

On **Linux**, this will use LVM and a set of shell scripts to obtain the `vgroup` and manipulate it. These scripts are located in the source folder `lvmscripts` and are named:

* `find-volume.sh`: Locates the volume where the given folder path is in.
* `create-lvm-snapshot.sh`: Creates the LVM snapshot and returns the path to it.
* `remove-lvm-snapshot.sh`: Removes a created snapshot

Usually, the operations require elevated privileges, for example root permissions.

For **MacOS**, the snapshots are not currently supported.

