---
description: This page provides an overview of how disaster recovery is possible
---

# Disaster Recovery

Duplicati is designed to be very careful when making backups, ensuring that all details are correct. At the same time it is designed to be very robust when performing restores, even when the data is partially incorrect.

However, in a real emergency, it is important that data can be extracted from the backup. Data should be extracted, even if this situation happens in the future where the original client can no longer run on your systems, or if large parts of the backup files are damaged.

Since the core restore algorithm is quite simple, albeit a bit slow, Duplicati has both a [recovery Python script](https://github.com/duplicati/duplicati/tree/master/Tools/Commandline/RestoreFromPython) for restoring, as well as a [C# recovery tool](../../duplicati-programs/command-line-interface-cli-1/recoverytool.md). Altough both are distributed as part of Duplicati, they are developed independet of the main programs. These two tools require some extra space and a manual process, but are generally much simpler in implementation, making them less prone to errors and more robust in the face of uncertain data.
