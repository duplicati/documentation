---
description: This page describes how to restore files using the Duplicati user interface
---

# Restoring files

The most important reason to make a backup is the ability to recover the data at a later stage, usually due to some unforseen incident. Depending on the incident, the original configuration may not be available.

To start a restore process in Duplicati, start on the "Restore" page.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 14.09.21.png" alt=""><figcaption><p>Choosing a restore approach</p></figcaption></figure>

If the backup configuration is already existing on the machine, you can choose it from the list below the two options for not having a configuration. In this case you can click "Next" and skip to the section on [choosing the files to restore](restoring-files.md#choosing-files-to-restore).&#x20;

The restore and browsing process are fastest when using a configured backup, because Duplicati can query a local database with information. If the local database is not present, Duplicati needs to fetch enough information from the remote storage to build a partial database when performing the restore.

If you have exported the backup configuration and have the configuration available, you click "Next" and skip to the [restore from configuration section](restoring-files.md#restore-from-configuration). You can also read up on how to [import and export configurations](../detailed-descriptions/import-and-export-backup-configurations.md).

## Direct restore from backup files

To restore files from the backup, Duplicati needs only to know how to access the files and the encryption passphrase (if any). If you do not have the passphrase, it is not possible to restore.

To restore directly from the backup files, the first step is to provide the destination details. These details are the same as you supplied initially when creating the backup. if you are using a cloud provider, you can usually get the needed information via your account on the vendors website.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 14.21.35.png" alt=""><figcaption><p>Supply storage destination details</p></figcaption></figure>

Once the details are entered, it is recommended to use the "Test connection" button to ensure that the connection is working correctly. Then click the "Next" button.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 14.22.46.png" alt=""><figcaption><p>Supply the encryption passphrase</p></figcaption></figure>

If the backup is not encrypted, leave the field empty. When ready, click "Connect" and Duplicati will examine the remote destination and figure out what backups are present. After working through the information, you can [choose files to restore](restoring-files.md#choosing-files-to-restore).

## Restore from configuration

If you have a configuration file you can use the information in that file to avoid entering it manually. If you need to restore more than once, it may be faster to [import the configuration](../detailed-descriptions/import-and-export-backup-configurations.md) and [rebuild the local database](../detailed-descriptions/the-local-database.md). After the database is built, you can choose the configuration from the list and skip to [choosing files to restore](restoring-files.md#choosing-files-to-restore).

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 14.39.27.png" alt=""><figcaption><p>Restoring with a backup configuration</p></figcaption></figure>

In the dialog, provide the exported configuration file and the configuration file's encryption passphrase. Note that the passphrase the configuration file is encrypted with is not neccesarily the same as the passphrase used to encrypt the backup with.

Once the configuration is correct, click the "Import" button and you are given the option to correct the settings before starting the restore process. If you do not need to change anything, click "Next" and then "Connect".

## Choosing files to restore

Once Duplicati has a connection to the remote destination it will find all the backups that were made. It will then choose the most recent version and list the files from within that version. Use the "Restore from" dropdown to select the version to restore from, and use the search field to highlight files matching the expression. Click the "Search" button to list only files matching the criteria.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 15.05.28.png" alt=""><figcaption><p>Choosing files to restore</p></figcaption></figure>

Check the files or folders that you want to restore and then click "Continue".

## Choosing restore options

When restoring there are a few options that control how the files are restored.

<figure><img src="../.gitbook/assets/Screenshot 2024-11-14 at 15.08.52.png" alt=""><figcaption><p>Choosing restore options</p></figcaption></figure>

If you want to restore a file to a previous state, you can leave the settings to their defaults. If you are unsure if you want to revert, or need to examine the files before replacing the current versions, you can choose to restore to another destination. If the folder you are restoring to is not empty, you can choose to store multiple versions of the files by appending the restore timestamp to the filename. This is especially useful if you are restoring multiple versions into a target folder for comparison.

Duplicati will not restore permissions by default because the users and groups that were present on the machine that made the backup may not be present on the machine being restored to. Restoring the permissions can cause you to be unable to access the restored files, if your user does not have the necessary permissions.

When satisfied with the settings, click the "Restore" button and the restore process will restore the files.
