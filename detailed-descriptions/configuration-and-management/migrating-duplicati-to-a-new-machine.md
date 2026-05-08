---
description: This page describes how to best migrate a Duplicati instance to a new machine
---

# Migrating Duplicati to a new machine

If you have moved to a new machine and want to restore files to the new machine, you can follow the steps outlined in [Restoring files](../../getting-started/restoring-files.md). If instead, you have already moved files to the new machine and would like to set up the new machine to continue backups made on the previous machine, there are a few ways to do this.

**Note:** it is possible to restore files across operating systems, but due to path differences it is not possible to continue a backup made on Windows on a Linux/MacOS based operating system and vice versa.

**Note:** do not attempt to run backups from two different machines to the same destination. Before migrating, make sure the previous machine is no longer running backups automatically. If both machines run backups, one instance will detect that the remote destination has been modified and will refuse to continue until the local database has been rebuilt.

If you have access to backup configurations, jump to the [section for moving with backup configurations](migrating-duplicati-to-a-new-machine.md#backup-configurations-are-available). And if you have no configurations, jump to the [manual setup section](migrating-duplicati-to-a-new-machine.md#previous-machine-and-configurations-are-unavailable).

## Previous machine is still available

If the previous machine is still accessible, you can copy over the contents of the `Duplicati` folder containing the the configuration database `Duplicati-server.sqlite` and the other support database. This approach is by far the fastest as Duplicati has all the information and does not need to check up with the remote storage.

{% hint style="info" %}
From version 2.3 the default is to encrypt the configuration database, and you need to decrypt it before copying. If you have disabled database encryption, you can skip the decryption step.
{% endhint %}

Before making a copy you need to decrypt the settings database so it can be read on the new machine. The new machine will encrypt it when Duplicati starts, using a new unique key for that machine. To decrypt the database, follow these steps on the machine you are moving from:

1. Stop Duplicati
2. Start Duplicati with the commandline option `--disable-db-encryption=true`
3. Stop Duplicati again

Make sure Duplicati is stopped before moving in the folder into the same location on the new machine. After moving in the folder, you can start Duplicati on the new machine and everything will be working as before. If it has been a while since the previous instance was running, this may trigger the scheduled backups on startup. Use the option `--startup-delay=5min` to start Duplicati in pause mode for 5 minutes if you want to check up before it starts running.

## Backup configurations are available

If you don't have the backup configurations, see the section on [import/export configuration](import-and-export-backup-configurations.md) for a guide on how to create the backup jobs from the configuration files.

With the backup configurations, it is possible to re-create the backup configurations. The flow allows you to modify set setup before saving the configuration, in case some details have changed. Once the backup is re-created, it is required that you run the repair operation to make Duplicati recreate the [local database](../../database-and-storage/the-local-database.md) for the backup.

Once the local database has been recreated, it is then possible to run the backup as before with no modifications required.

## Previous machine and configurations are unavailable

If you do not have access to the previous setup, you can still continue the backups, but this requires that you re-create the backups manually. You need at least the storage destination details, the passphrase and to select the sources.

Once the backup configuration has been created it works the same as if you had imported it from a file. Before running a backup, you need to run the repair operation to make Duplicati recreate the [local database](the-local-database.md) for the backup.

Once the local database has been recreated, it is then possible to run the backup as before with no modifications required.
