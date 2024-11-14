---
description: This page describes how to run a backup outside of an automatic schedule
---

# Running a backup

With a configured backup, you can have a schedule that runs the backup automatically each day. Having the backup run automatically is recommended because it makes it less likely that the backups are not recent if they are needed.

Even if the backup already has a schedule there may be times where you want to manually run a backup. If you have just configured a backup, you may want to run it ahead of the scheduled next run. If you are within the UI you can click the "Run now" link for the backup.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 14.44.43.png" alt=""><figcaption><p>A newly configured backup</p></figcaption></figure>

Once the backup is running, the top area will act as a progress bar that shows how the backup progresses. Note that the first run of a backup is the slowest run because it needs to process every file and folder that is part of the source. On later runs it will recognize what parts have changed and only process the new and changed data.

If you need to automate starting a backup without using the UI, you can use [ServerUtil](../duplicati-programs/command-line-interface-cli/serverutil.md) to trigger backups from the commandline.

After running a backup, the view will change slightly and show some information about the backup.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 09.21.50.png" alt=""><figcaption><p>After the backup has completed it shows backup details</p></figcaption></figure>
