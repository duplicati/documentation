---
description: >-
  Let Duplicati monitor the source folders of a backup between runs, so the
  next backup only examines the files that changed instead of scanning
  everything
---

# File system watcher

By default, every backup run walks through all files and folders in the source and checks each one for changes. For large sources with few changes, this scan can take most of the backup time and cause a lot of disk activity.

The file system watcher is an alternative. When it is turned on for a backup, the Duplicati server keeps a watcher on the local source folders between runs and records each path that changes. When the backup runs, it gets the list of changed paths and examines only those. Everything else is carried over from the previous backup.

This trades a little background work while no backup runs for much less disk activity when one does.

{% hint style="info" %}
The file system watcher was added in canary 2.4.1.102 and is an experimental feature. It is off by default.
{% endhint %}

## Enabling the watcher for a backup

The watcher is turned on for each backup with the advanced option `--use-file-watcher`:

1. Edit the backup and go to the **Options** step.
2. Under **Advanced options**, add `use-file-watcher` and turn it on.
3. Save the backup.

The option is only read from the backup's own settings. Setting it in the default options for all backups has no effect, so it must be added to each backup that should use the watcher.

The watcher only monitors local source folders. It is used by backups that run in the Duplicati server, not by the command line.

## When a full scan is still done

The watcher can only skip the scan when it has seen every change since the previous backup. A backup scans all files when:

* It is the first backup after the server started, as no changes were recorded while the server was not running.
* It is the first backup after the backup configuration was edited.
* It is the first backup after the watcher was turned on.
* The watcher saw an event it cannot vouch for, so it cannot be sure the list of changes is complete.
* More paths changed since the previous backup than the limit set with `--file-watcher-max-changes`. At that point a full scan is cheaper than going through the list.

Each later run uses the list again.

The watcher is not used at all, and the backup scans as before, when the backup:

* Uses the USN journal on Windows (`--usn-policy`), which already tracks changes.
* Follows symbolic links.
* Excludes empty folders.
* Backs up alternate data streams.
* Uses a hardlink policy other than `all`.

## Turning the watcher off for all backups

The watcher can be turned off for every backup at once, regardless of the setting of each backup. This is useful if the watcher causes problems, or to compare backup behavior with and without it.

In the user interface, open **Settings**, find **Experimental options**, and switch off **File system watcher**. The label then reads "The file watcher is disabled for all backups".

The same can be done when starting the server:

```
duplicati-server --disable-file-watcher=true
```

## Tuning the watcher

The watcher keeps the changes it records in memory, and writes them to a tracking database at intervals. These server options control how it does that. They are given when starting the server, and the defaults are fine for most setups.

| Option | Description |
| --- | --- |
| `--file-watcher-flush-interval` | How often the watcher writes the changes it holds in memory to its tracking database. The value is a [duration](../../technical-details/option-formats.md#timespans-timestamps-and-durations). |
| `--file-watcher-high-water` | The number of changed paths the watcher holds in memory before writing them to its tracking database ahead of the flush interval. |
| `--file-watcher-max-changes` | The number of changed paths recorded between two backups above which the next backup scans all files instead of using the list. |
| `--file-watcher-grace-delay` | How long a backup waits for late file system events after it stops the recording, before it reads the change list. Changes seen within this window before the snapshot are reported to both the current and the next backup, so none are lost. |

## Related options

* `--changed-files` lets a script give a backup the list of changed files, for setups where another tool already knows what changed.
* `--usn-policy` uses the NTFS change journal on Windows, which also avoids a full scan, and is kept by the file system while Duplicati is not running.
