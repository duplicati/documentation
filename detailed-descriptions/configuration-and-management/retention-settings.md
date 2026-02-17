---
description: This page describes the different retention settings available in Duplicati
---

# Retention settings

Even though Duplicati tries hard to reduce storage use as much as possible, it is inevitable that the remotely stored data grows as new versions of files are added. To avoid running out of space or paying for excessive storage use, it is important that unnecessary backups are removed regularly.

In Duplicati there are a few different settings that can be used to configure when a "snapshot" is removed. All of these options are invoked automatically at the end of a backup to ensure that removal follows a new version. If you use the [Command Line Interface](../../duplicati-programs/command-line-interface-cli.md), it is possible to disable the removal and run the delete command as a separate step.

After deleting one or more versions, Duplicati will mark any data that can no longer be referenced as waste, and may occasionally choose to run a compact process that deletes unused volumes and creates new volumes with no wasted space.

Despite all deletion rules, Duplicati will never delete the last version, keeping at least one version available.

## Delete older than

The most intuitive option is to choose a period that data is stored, and then to consider everything older than this period as stale data. The actual period depends on the actual use, but it could be 7 days, 1 year or 5 years for example.

This option is usually the prefered choice if the backups happen regularly, such as a backup each day, and then keep the last 3 months.

## Keep versions

If the backups are running irregularly, where the backups are triggered by some external event, there may be long periods where there are no backups. For this case you can choose a number of versions to keep and Duplicati will consider anything outside that count as outdated.

Another special case is that if the source data has not changed at all, which is uncommon, Duplicati will not make a new version, as it would be identical to the previous version. In such a setup, it may be preferable to use a version count, despite regularly scheduled backups.

## Retention policy

The retention policy is a "bucket" based strategy, where you define how many backups to keep in each "bucket" and what a "bucket" covers. With this strategy, it is possible to get something similar to [grandfather-father-son](https://en.wikipedia.org/wiki/Backup_rotation_scheme#Grandfather-father-son) style backup rotations.

The syntax for the rentention policy uses the [time format](../../technical-details/option-formats.md#timespans-timestamps-and-durations) to define the bucket and contents in that bucket. The bucket size is first, then a colon separator, and then the duration in the bucket. Multiple buckets can be defined with commas. As an example:

```
7D:U,1Y:1W
```

The first bucket is defined as being 7 days, and the value `U` means unlimited the number of backups in this bucket. In other words: for the most recent 7 days, keep all backups.

The second bucket is defined as 1 year, keeping a backup for each 1 week, resulting in rougly 52 backups after the first 7 days.

Any backups outside the buckets are deleted, meaning anything older than a year would be removed.

In the UI, a helpful default is called "Smart retention" which sets the following retention policy:

```
1W:1D,4W:1W,12M:1M
```

Translated, this policy means that:

* For the most-recent week, store 1 backup each day
* For the last 4 weeks, store 1 backup each week
* For the last 12 months, store 1 backup each month
